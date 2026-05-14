---
name: lint
description: Global health check for the persistent wiki knowledge base. Scans wiki/ to detect dead links, orphan pages, unsynced index entries, unresolved knowledge conflicts, stale claims, and missing high-value concept pages. Invoked when the user types /lint, /scan, /health, or asks to check wiki health.
user-invocable: true
---

# lint Skill: Knowledge Graph Health Check

## Core Objective
Apply static-analysis style checks to the wiki so it stays accurate, interlinked, and compounding over time. Focus on structural integrity, contradiction visibility, and maintenance gaps that reduce future query quality.

## Trigger Conditions
- User types `/lint` or `/scan` or `/health`
- User asks "how healthy is my knowledge base"
- User asks to "check the knowledge base status" or "check health"

## Knowledge Base Path
- Use the Glob tool to dynamically locate the `wiki/` directory under the current workspace

## Operational Files (Excluded from Content Checks)
The following files are infrastructure, not knowledge pages. Exclude them from index-consistency, dead-link, and orphan checks:
- `wiki/index.md`
- `wiki/log.md`
- `wiki/ingest-manifest.md`

## Inspection Pipeline

### Step 1: Index Consistency Check
1. Read the full content of `wiki/index.md`.
2. Scan all `.md` files under `wiki/` **excluding the three operational files listed above**.
3. Extract all wikilinks `[[PageName]]` registered in `index.md`.
4. Compare and report:
   - Index entries whose target files do not exist → **dead index entry**
   - Files that exist but are not registered in the index → **unsynced page**
   - Pages categorized under the wrong section (Concepts, Sources, Syntheses)

### Step 2: Wikilink Health Check
1. Scan all `.md` files (including operational files as link sources) and extract all `[[wikilink]]` format links.
2. For each link, resolve the target: a wikilink `[[PageName]]` resolves to `wiki/concepts/PageName.md`, `wiki/sources/PageName.md`, or `wiki/syntheses/PageName.md`. If none of those exist, mark as **dead link**.
3. Build a reference count for each page (how many other pages link to it), excluding self-references and links from operational files.
4. Pages with a reference count of zero that are not operational files → **orphan pages**.
5. For each knowledge page (non-operational), verify `## Related Links` exists and is non-empty.

### Step 3: Cognitive Conflict Audit
1. Search all `.md` files globally for pages containing `## Knowledge Conflict`.
2. Extract a brief description of each conflict (what claims disagree and which sources support them).
3. Mark conflicts as unresolved when no comparison or resolution note is present.
4. Count unresolved conflicts as knowledge debt.

### Step 4: Freshness and Drift Audit
1. Identify pages with old `last_updated` dates that are heavily linked or frequently cited.
2. Flag claims likely superseded by newer source pages (based on source dates and wording such as "latest", "current", "always").
3. Report pages needing refresh to keep synthesis current.

### Step 5: Coverage Gap Audit
1. Extract frequently mentioned concepts from wiki pages.
2. Detect concepts repeatedly referenced in prose but missing dedicated concept pages.
3. Suggest candidate pages and likely parent links.

### Step 6: Encoding Integrity Check
Scan all wiki `.md` files for mojibake sequences. Use Python via Bash:
```bash
python -c "
import os, glob
patterns = ['Ã¢', 'Ã¢']
hits = []
for path in glob.glob('wiki/**/*.md', recursive=True):
    with open(path, 'r', encoding='utf-8') as f:
        content = f.read()
    if any(p in content for p in patterns):
        hits.append(path)
for h in hits:
    print(h)
"
```
Report any files containing known mojibake sequences as **encoding errors** (Red). If found, recommend running the byte-pattern replacement fix described in CLAUDE.md.

### Step 7: Optional Next-Source Suggestions
1. From unresolved conflicts and missing coverage, suggest what new source types would reduce uncertainty.
2. Keep suggestions concrete and tied to named pages.

## Report Output Specification

After scanning, output a structured report strictly following this format:

```markdown
## Knowledge Base Health Report — YYYY-MM-DD

### Green Items
- [Items that are working well]

### Yellow Items
- **Found N orphan pages**: [list] — recommend adding links or categorizing
- **Found N unsynced index entries**: [list] — files exist but not registered in index.md
- **Found N stale pages**: [list] — recommend refresh with newer sources
- **Found N missing concept pages**: [list] — recommend creating concept pages

### Red Items
- **Found N dead links**: [source page] -> [[non-existent target page]]
- **N unresolved knowledge conflicts**: [page names]
- **N encoding errors**: [page names] — mojibake detected; run byte-pattern fix from CLAUDE.md

### Next Actions
1. Should I apply safe auto-fixes (index sync entries, obvious dead-link typos, Related Links backfills)?
2. Should I draft refresh tasks for stale pages?
3. Should I propose source-ingest priorities for unresolved conflicts?
```

## Hard Constraints
- **Read-only scan**: Do not modify, delete, or rename any file before generating the report
- **Manual confirmation**: Wait for user confirmation before executing any fixes
- **No raw-layer writes**: Never modify files under `raw/`
- **Logging rule**: After the report is generated (before or after fixes), append to `wiki/log.md` with action `lint` and a concise result summary. Use Bash `rm -f` for any file deletions approved by the user; use Python via Bash for body-text edits in files with non-ASCII content (see CLAUDE.md Environment & Tooling Notes).
