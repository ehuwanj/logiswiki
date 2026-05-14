# Language Settings & Core Role (Global Rules)
- **Language directive**: Regardless of input language, you must always use **English** for thinking, replies, and all wiki writing.
- **Role definition**: You are maintaining an **LLM Wiki** (per [Karpathy's spec](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)), and your task is to compile fragmented information into a structured, highly cross-linked Obsidian knowledge base.

# LLM Wiki Operating Principles (Karpathy-Aligned)
- The wiki is a **persistent, compounding artifact**. Avoid re-deriving knowledge from raw sources on every query.
- Prefer **incremental maintenance**: ingest updates existing wiki pages, query can produce durable syntheses, lint keeps the graph healthy.
- Prioritize **evidence-grounded synthesis** over generic summaries. Preserve uncertainty and contradictions explicitly.
- Keep navigation and history first-class by maintaining `wiki/index.md` and `wiki/log.md` on every relevant operation.

# Core Directory & Permission Boundaries (Immutability & Architecture)
You must strictly observe the following file operation permissions — these are inviolable constraints:

- `/raw/` (Immutable Layer):
  - **Absolutely read-only**. This stores my raw materials, web clippings, and media content.
  - **Never modify, move, or delete any file in this directory**. It is the single source of truth.
- `/assets/` (Media Assets Layer):
  - Stores images, PDFs, and media. Reference using Obsidian standard syntax `![[filename.png]]`.
- `/wiki/` (Compiled Output Layer - You Own This):
  - This is your dedicated workspace. Create, update, refine knowledge, and resolve conflicts here.

# Wiki Core File Contract (The Wiki Schema)
When working in `/wiki/` (especially after write operations), you must maintain the following cornerstones:

1. **`wiki/index.md` (Master Index)**:
   After adding any new knowledge page to the wiki, you must synchronously update this file and add the entry under the correct category.
   Format: `[[PageName]] - one-sentence description.`
  - Concepts: use TitleCase naming.
  - Sources/Syntheses: use kebab-case naming.
    Example:
    ```markdown
    # Wiki Index

    ## Sources
    - [[knowledge-source-slug]] - restructured description of the source material.

    ## Concepts
    - [[ConceptName]] - Definition of this concept.

    ## Syntheses
    - [[synthesis-slug]] - The complex question this wiki answers.
    ```
2. **`wiki/log.md` (Operation Log)**:
    Append-only. Record after every operation: `## [YYYY-MM-DD] <action> | <brief description>`.
    Action types: ingest, query, lint
    Example:
    ```markdown
    ## [2026-04-11] ingest | Ingested Claude Code core concepts
    - **Changes**: Added [[ClaudeCode]], [[summary-claude-code-docs]]; updated [[index.md]]
    - **Manifest**: Processed N files, skipped M unchanged, missing K, pruned P
    - **Conflicts**: None (or: Conflict in [[RAGArchitecture]], flagged)

    ## [2026-04-11] lint | Weekly health check
    - **Result**: Fixed 2 dead links, found 1 orphan page [[UnlinkedPage]]
    ```
3. **`wiki/ingest-manifest.md` (Incremental Ingest State)**:
    Keep an append/update manifest so ingest can skip unchanged raw files and avoid re-reading all sources.
    Required fields per source:
    - `raw_path`: path under `raw/`
    - `fingerprint`: lightweight file fingerprint (size + modified time; hash optional)
    - `last_ingested`: YYYY-MM-DD
    - `source_page`: wiki source page created for this raw file
    - `status`: active | skipped | error | missing | pruned
    Rules:
    - `/ingest` must process only files that are new or changed relative to the manifest.
    - `/ingest <path>` should skip unchanged files unless user explicitly asks to reingest.
    - `/ingest` (without prune) must mark manifest entries as `missing` when `raw_path` no longer exists, but must not delete wiki pages.
    - `/ingest --prune` may clean up wiki artifacts for `missing` sources in a safe, logged way.
    - Every ingest run must update manifest entries for files it touched.
4. **Mandatory Bidirectional Links**:
   Every wiki page must include a `## Related Links` section, using Obsidian wikilinks `[[PageName]]` to connect to related concepts. Isolated pages are never acceptable.
5. **Conflict Resolution Principle**:
   If newly ingested knowledge conflicts with existing knowledge, do not silently overwrite. Create a `## Knowledge Conflict` section on the page, preserve both versions, and compare them.

# Local Skill Delegation (Authoritative)
The following local skills already exist and should be used as the primary workflow implementations:

- **ingest** (`.claude/skills/ingest/SKILL.md`): compile source material from `raw/` into `wiki/`, incrementally update concept/source pages, maintain cross-links, update index/log, preserve conflicts.
- **query** (`.claude/skills/query/SKILL.md`): answer from `wiki/` only (index-first retrieval), provide evidence with wikilinks, and offer to persist high-value answers as syntheses.
- **lint** (`.claude/skills/lint/SKILL.md`): run wiki health checks for dead links, orphan pages, index drift, unresolved conflicts, stale claims, and coverage gaps.

If any instruction in this file conflicts with a local skill implementation detail, resolve it in favor of these shared invariants:
- raw layer immutability
- incremental ingest by manifest
- bidirectional linking
- explicit conflict preservation
- index/log maintenance
- English output

# Workflow Instructions (Workflows / Skills)
When asked to perform the following operations, invoke and follow the corresponding local skill:

- `/ingest <path>` or `/ingest`: Use the **ingest** skill workflow. Integrate new information into existing wiki pages (compounding behavior), preserve conflicts under `## Knowledge Conflict`, update index/log, and maintain `wiki/ingest-manifest.md` so unchanged raw files are skipped.
- `/ingest --prune`: Use the **ingest** skill workflow in safe prune mode. Only prune sources whose `raw_path` is missing, remove stale wiki/source references, remove concept pages that lose all source provenance due to prune, update index/log, and set manifest status to `pruned`.
- `/query <question>`: Use the **query** skill workflow. Read `wiki/index.md` first, then deep-read relevant pages, answer with `[[wikilinks]]`, and separate wiki-grounded facts from general fallback when necessary.
- `/lint`: Use the **lint** skill workflow. Audit link integrity, index consistency, unresolved conflicts, staleness, and missing concept coverage; report findings before applying any fixes.

# Page Frontmatter (YAML) Specification
All generated wiki pages must include the following YAML header:

```yaml
---
title: "Page Title"
type: concept | source | synthesis
tags: [knowledge-tags]
sources: [list of supporting raw/wiki files, with the original source URL embedded as a Markdown link when available]
last_updated: YYYY-MM-DD
---
```

**`sources` field rule**: When a raw file's own frontmatter contains a `source:` URL (typical for web clippings), the entry in the wiki page's `sources` array must be written as a Markdown link `[raw/path/to/file.md](https://original-source-url)` so the raw path is clickable and navigates to the original source. Entries without a known URL remain plain paths. Wrap each Markdown-link entry in double quotes because it contains YAML-significant characters (`:`, `[`, `]`, `(`, `)`).

# Environment & Tooling Notes
This wiki runs on **Windows 11**. Claude Code is typically started from **Git Bash**, so the Bash tool runs POSIX bash and POSIX commands work. Apply the following rules in all skill workflows:

## Shell Tool Usage
- **Bash tool (primary)**: Use for all file system operations. POSIX commands work because Claude is started from Git Bash:
  - Delete files: `rm -f "wiki/concepts/File.md"`
  - List files: `ls wiki/concepts/`
  - Python scripts: `python -c "..."`
- **PowerShell tool (fallback)**: Use only when the Bash tool is unavailable or explicitly needed. Equivalent operations: `Remove-Item -Force`, `Get-ChildItem`.
- **Edit tool**: Reliable for YAML frontmatter and ASCII-safe content. May fail to match non-ASCII characters in page body text if encoding variance exists in the file.
- **Python via Bash**: Use for any body-text manipulation in existing wiki files that contains non-ASCII characters. Example pattern:

  ```bash
  python -c "
  path = 'wiki/concepts/PageName.md'
  with open(path, 'r', encoding='utf-8') as f:
      lines = f.readlines()
  filtered = [l for l in lines if '[[TargetLink]]' not in l]
  with open(path, 'w', encoding='utf-8') as f:
      f.writelines(filtered)
  "
  ```

## File Fingerprinting
To compute a fingerprint (size + mtime) for manifest entries, use Python via Bash:

```bash
python -c "
import os, datetime
s = os.stat('raw/filename.md')
mtime = datetime.datetime.fromtimestamp(s.st_mtime).strftime('%Y-%m-%dT%H:%M:%S')
print(f'{s.st_size} / {mtime}')
"
```

## Character Safety Rule (Encoding)

**Use ASCII-only characters in all wiki content.** Non-ASCII characters (`—`, `–`, `→`, smart quotes, etc.) have been confirmed to produce double-encoded mojibake on disk when written through the Claude Code toolchain on Windows. The mojibake is NOT a display artefact — it is real corrupt bytes that render as garbage in Obsidian.

Mandatory substitutions:
| Avoid | Use instead | Context |
|-------|-------------|---------|
| `—` (em-dash) | ` - ` (space-hyphen-space) | Related Links separators, inline prose |
| `–` (en-dash) | `-` (hyphen) | Numeric ranges (`50-150 MB`) |
| `→` (right arrow) | `->` | Flow diagrams, pipelines |
| `"` `"` (curly quotes) | `"` (straight quote) | Any quoted text |
| `'` `'` (curly apostrophes) | `'` (straight apostrophe) | Any possessives |

If mojibake is discovered in existing wiki files (patterns like `Ã¢â‚¬â€` or `Ã¢â€ â€™`), fix using Python via Bash with the targeted byte-pattern replacement approach. Pre-2026-05-14 wiki pages were affected and have already been patched.
