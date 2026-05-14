---
name: ingest
description: Compiles raw materials from the raw/ directory into a persistent, interlinked wiki/. Supports `/ingest` (scan pending files under raw/), `/ingest <path>` (process one file), and `/ingest --prune` (safe cleanup for sources removed from raw/). Uses `wiki/ingest-manifest.md` to skip unchanged files and reduce token usage. Also triggered when the user mentions ingesting, importing, or adding material to the knowledge base. Raw files are immutable and must never be modified, moved, or deleted.
user-invocable: true
---

# ingest Skill

## Core Workflow: Compile and Maintain

You are maintaining an **LLM Wiki** (Obsidian knowledge base). The `raw/` directory is the immutable source layer, and `wiki/` is the compiled, continuously maintained knowledge layer.

**Directory structure conventions:**
- `raw/` - source documents (read-only, single source of truth)
- `raw/archived/` - optional legacy folder; do not use for active ingest
- `wiki/` - compiled wiki pages (concepts, sources, syntheses)
- `wiki/ingest-manifest.md` - incremental ingest state for skip decisions

## Trigger Logic

1. **User runs `/ingest`**: Scan files under `raw/` (excluding `raw/archived/`) and process only sources marked new or changed by the manifest.
2. **User runs `/ingest <path>`**: Process only the specified source file if it is new or changed; otherwise skip unless the user explicitly requests reingest.
3. **User runs `/ingest --prune`**: Run ingest plus safe prune cleanup for manifest entries whose `raw_path` no longer exists.
4. **Implicit trigger**: When the user asks to ingest, import, or add material into the wiki, execute ingest automatically.

## Command Semantics

- `/ingest`: Ingest only new/changed files; detect deleted raw sources and mark them as `missing` in the manifest without deleting wiki content.
- `/ingest <path>`: Ingest one file (skip unchanged unless explicit reingest).
- `/ingest --prune`: Execute safe cleanup for `missing` sources, then mark those rows `pruned`.

## Compilation Pipeline

For each pending source file, execute the following steps strictly in order:

### Step 0: Manifest-Based Candidate Selection

1. Read `wiki/ingest-manifest.md` if it exists; if missing, create it with an empty table.
2. List candidate files under `raw/` excluding `raw/archived/`. Use Bash:
   ```bash
   find raw/ -type f ! -path '*/archived/*'
   ```
3. Build a lightweight fingerprint per file using size + modified time. Use Python via Bash:
   ```bash
   python -c "
   import os, datetime
   s = os.stat('raw/filename.md')
   mtime = datetime.datetime.fromtimestamp(s.st_mtime).strftime('%Y-%m-%dT%H:%M:%S')
   print(f'{s.st_size} / {mtime}')
   "
   ```
4. Mark files as pending only when no manifest entry exists, fingerprint changed, or user requested reingest.
5. Detect manifest rows whose `raw_path` no longer exists; set status to `missing` (unless already `pruned`) and include missing counts in the ingest summary.
6. Skip unchanged files and include skip counts in the ingest summary.

### Step 0.5: Safe Prune Planning (`/ingest --prune` only)

Build prune candidates from manifest rows where:
- `raw_path` is absent from the current `raw/` scan
- status is `missing` or previously active/skipped/error with now-missing path

For each prune candidate, prepare this cleanup plan:
- source page to remove (from `source_page` field)
- index entries to remove from `wiki/index.md`
- pages that still link to the source page
- pages that reference the missing `raw_path` in frontmatter `sources`

### Step 1: Read Source File

- **If `.md` file**: Use the Read tool to read the full content.
- **If `.pdf` file**: Use the Read tool to attempt text extraction.
- **If source references images/assets**: read text first, then inspect relevant assets as needed.

### Step 2: Distill Core Content

Extract from the source file:
- **Concepts**: products, tools, organizations, methods, and other key terms.
- **Claims**: key statements and conclusions from the source.
- **Evidence**: data points, examples, and supporting rationale.
- **Open questions**: unresolved points worth tracking.

If the content is in a non-English language, translate it to English.

### Step 3: Create restructured knowledge pages

Before writing the page, read the raw file's own YAML frontmatter and capture its `source:` URL (typical for web clippings). If present, write the entry in `sources` as a Markdown link `[raw/path.md](https://url)` (quoted) so the raw path is clickable and navigates to the original source. If the raw file has no URL, use the plain path.

Create a Markdown file in `wiki/sources/`:

```markdown
---
title: "Human-Readable Source Title"
type: source
tags: [source, raw-material]
sources:
  - "[raw/xxx.md](https://original-source-url)"
last_updated: YYYY-MM-DD
---

## Summary
[3–5 sentence core summary]

## Key Claims
- [Claim 1]

## Evidence and Notes
- [Evidence or supporting details]

## Related Links
- [[ConceptName]] - related concept
```

Use kebab-case for filenames: `knowledge-source-slug.md`

### Step 4: Knowledge Networking (Concept Pages)

For each concept extracted in Step 2:

**Target directories:**
- Concepts → `wiki/concepts/`

**Processing logic:**
1. Page does not exist → create a new page following the Frontmatter spec in CLAUDE.md
2. Page already exists → read existing content and incrementally merge new information
3. Add or refresh cross-links so source pages and concept pages reference each other
4. **Conflict detected** → do not overwrite; preserve both versions under a `## Knowledge Conflict` section with source attribution

**Page template:**

For each raw file referenced in `sources`, if the raw file's frontmatter has a `source:` URL, write the entry as a Markdown link `[raw/path.md](https://url)` (quoted) instead of a plain path.

```markdown
---
title: "Human-Readable Page Name"
type: concept
tags: [tags]
sources:
  - "[raw/path-to-source.md](https://original-source-url)"
last_updated: YYYY-MM-DD
---

## Definition
[Definition of this concept]

## Key Information
[Detailed information extracted from source files]

## Knowledge Conflict
[Only include this section when conflicting claims exist.]

- Existing view: [prior claim with citation]
- New view: [incoming claim with citation]
- Comparison: [why they differ or what remains uncertain]

## Related Links
- [[summary-source-slug]] - source
- [[RelatedConcept]] - related concept
```

### Step 5: Update Global Registry

**Update `wiki/index.md`:**
Add newly created pages to the correct category following the format specified in CLAUDE.md:
- Sources: `[[summary-source-slug]] - core thesis of the material`
- Concepts: `[[ConceptName]] - Definition of the concept`
- Syntheses (if created): `[[synthesis-slug]] - complex question and synthesized answer`

**Update `wiki/log.md`:**
Append operation log (append-only):
```markdown
## [YYYY-MM-DD] ingest | Brief description
- **Changes**: Added/updated [[PageName]]; updated [[index.md]]
- **Manifest**: Processed N files, skipped M unchanged, missing K, pruned P
- **Conflicts**: None (or: Conflict in [[ConflictingPage]], paused awaiting decision)
```

**Update `wiki/ingest-manifest.md`:**
- Upsert one row per candidate file with: raw path, fingerprint, last ingested date, source page, status.
- Use `status: active` for processed files and `status: skipped` for unchanged files.
- Use `status: missing` when a manifest row points to a deleted raw source and prune mode is not requested.
- Use `status: pruned` after successful prune cleanup.
- Update existing entries in place to avoid duplicates.

### Step 5.5: Safe Prune Execution (`/ingest --prune` only)

For each prune candidate, execute in this order:

1. **Delete the source page** using Bash `rm`:
   ```bash
   rm -f "wiki/sources/source-page-slug.md"
   ```
   If the Bash tool is unavailable, fall back to the PowerShell tool: `Remove-Item -Force "wiki\sources\source-page-slug.md"`.

2. **Remove the source entry from `wiki/index.md`** using the Edit tool (the index contains only ASCII-safe content).

3. **Remove links to the deleted source page** from `## Related Links` sections in other pages. Use the Edit tool if the target line is ASCII-safe. If the line contains non-ASCII characters (e.g., em-dashes), use Python via Bash:
   ```bash
   python -c "
   path = 'wiki/concepts/PageName.md'
   with open(path, 'r', encoding='utf-8') as f:
       lines = f.readlines()
   filtered = [l for l in lines if '[[deleted-source-slug]]' not in l]
   with open(path, 'w', encoding='utf-8') as f:
       f.writelines(filtered)
   "
   ```

4. **Remove the deleted raw path from frontmatter `sources` arrays** across `wiki/` pages. The Edit tool is reliable for frontmatter edits (ASCII-safe). Apply it to each affected page.

5. **Detect concept pages whose `sources` frontmatter array becomes empty** after step 4. Treat these as prune-owned concepts.

6. **For each prune-owned concept**: delete its file with `rm -f`, remove its entry from `wiki/index.md` with the Edit tool, and remove links to it from other pages using Python via Bash (step 3 pattern).

7. **Set manifest row status to `pruned`**, fingerprint to `missing`, and `last_ingested` to run date using the Edit tool.

### Step 6: Finalize Ingest State

After confirming all of the following are complete, finish ingest without modifying raw files:
- sources page created
- concept pages created or updated
- index.md updated
- log.md updated
- ingest-manifest.md updated
- prune cleanup applied when `/ingest --prune` is requested

**Never modify, move, or delete any file in `raw/`.**

## Conflict Resolution Flow

When a conflict between new and existing knowledge is found:

1. **Preserve**: Keep both versions in the target concept page under `## Knowledge Conflict`
2. **Attribute**: Cite which sources support each claim
3. **Compare**: Add a short comparison note describing differences and uncertainty
4. **Report**: Include the conflict in `wiki/log.md` and notify the user in the ingest summary

## Constraints

- Never modify, move, or delete files under `raw/`
- Exclude `raw/archived/` from normal ingest scans
- Use manifest state to avoid re-reading unchanged raw files
- Only run wiki deletions when user explicitly requests `/ingest --prune`
- All wiki pages must include a `## Related Links` section — isolated pages are not acceptable
- Write all content in English
- Concept names use TitleCase; sources and syntheses use kebab-case
- **ASCII-only characters in wiki content**: use ` - ` (not `—`), `->` (not `→`), `-` (not `–`). Non-ASCII chars cause double-encoded mojibake on disk on Windows — see CLAUDE.md Character Safety Rule.
- Use Bash tool with POSIX commands for file system operations (Claude is started from Git Bash); use Python via Bash for body-text edits in files with non-ASCII content; fall back to PowerShell tool if Bash is unavailable
