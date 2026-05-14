# Copilot Chat Instructions for LogisWiki

## Role & Context
You are assisting with maintenance of an **LLM Wiki** (per [Karpathy's spec](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)) — a persistent, compounding knowledge base compiled from raw materials into a structured, highly cross-linked Obsidian graph in the `/wiki/` directory.

**Note**: This file is a Copilot Chat adaptation of [.claude/CLAUDE.md](.\.claude\CLAUDE.md). For deeper technical implementation details, environment-specific patterns, and full skill integration workflows, refer to CLAUDE.md (used by Claude Code).

## Core Invariants (Non-Negotiable)
- **Language**: Always use English for responses and all wiki content
- **Raw layer immutability**: The `/raw/` directory is read-only. Never modify, move, or delete files there
- **Incremental ingest**: Use manifest-based workflow to skip unchanged files
- **Bidirectional linking**: Every wiki page must have a `## Related Links` section with wikilinks
- **Conflict preservation**: When knowledge conflicts, preserve both versions under `## Knowledge Conflict`
- **Index/log maintenance**: Always update `wiki/index.md` and `wiki/log.md` after write operations
- **ASCII-only content**: Avoid non-ASCII characters (—, –, →, smart quotes); use ASCII equivalents

## Directory Structure
```
/raw/                    <- IMMUTABLE: web clippings, raw materials
/assets/                 <- Media: images, PDFs (reference as ![[filename]])
/wiki/                   <- Your workspace: create, update, refine pages here
  /index.md              <- Master index (update after adding pages)
  /log.md                <- Operation log (append-only)
  /ingest-manifest.md    <- Incremental ingest state
  /concepts/             <- TitleCase named concept pages
  /sources/              <- kebab-case named source pages
  /syntheses/            <- kebab-case named synthesis pages
```

## Workflow Commands

### `/ingest` — Compile raw materials into wiki
- **`/ingest`** (no args): Scan pending files in `/raw/`, skip unchanged files per manifest
- **`/ingest <path>`** (single file): Process one specific raw file
- **`/ingest --prune`** (safe cleanup): Remove stale wiki artifacts for missing raw sources
- **Behavior**: Incrementally update concept/source pages, maintain cross-links, preserve conflicts, update index/log/manifest

### `/query <question>` — Answer from wiki only
- Read `wiki/index.md` first for keyword/semantic index
- Deep-read relevant pages that match the query context
- Answer with wikilink citations `[[PageName]]`
- Declare "Not found in local knowledge base" if no relevant wiki content exists
- Never use model memory; ground all claims in wiki evidence

### `/lint` — Wiki health audit
- Detect dead links, orphan pages, index drift, unresolved conflicts, stale claims, coverage gaps
- Report findings before applying any fixes
- Update `wiki/log.md` with results

## Wiki Page Frontmatter (Required)
All new/updated wiki pages must include YAML header:

```yaml
---
title: "Page Title"
type: concept | source | synthesis
tags: [knowledge-tags]
sources: [list of supporting raw/wiki files, with original source URLs as Markdown links]
last_updated: YYYY-MM-DD
---
```

**sources field rule**: When a raw file has a `source:` URL in its frontmatter, write the entry as `[raw/path/to/file.md](https://original-source-url)` so the path is clickable. Wrap Markdown-link entries in double quotes due to YAML special characters.

## Page Naming Conventions
- **Concepts**: `TitleCase` (e.g., `[[DebeziumConnector]]`, `[[MutualTls]]`)
- **Sources**: `kebab-case` (e.g., `[[route-finder-datahub-sync]]`)
- **Syntheses**: `kebab-case` (e.g., `[[key-distribution-approach-comparison]]`)

## Character Safety (Mandatory ASCII Substitutions)
Prevent mojibake corruption on Windows 11:

| Avoid | Use Instead | Context |
|-------|---|---|
| `—` (em-dash) | ` - ` (space-hyphen-space) | Prose, separators |
| `–` (en-dash) | `-` (hyphen) | Numeric ranges (e.g., `50-150 MB`) |
| `→` (right arrow) | `->` | Pipelines, flow diagrams |
| `"` `"` (curly quotes) | `"` (straight quote) | Quoted text |
| `'` `'` (curly apostrophes) | `'` (straight apostrophe) | Possessives |

## Priority Principle
When working in this wiki, your primary goal is **durable, evidence-grounded knowledge synthesis**, not generic summaries. Preserve uncertainty and contradictions explicitly. Treat the wiki as a compounding artifact — each operation should leave the graph healthier and more interconnected than before.

---

## For Full Wiki Automation: Use Claude Code

**Copilot Chat** is best for:
- Interactive `/query` operations to explore wiki knowledge
- Manual `/ingest` and `/lint` workflows following the patterns above
- Ad-hoc knowledge synthesis

**Claude Code** is best for:
- Full automation of `/ingest`, `/query`, `/lint` with local skill files
- Complex manifest-driven incremental ingest (`/ingest --prune`)
- Environment-specific operations (fingerprinting, bash scripting, Python)
- Maintaining and evolving the local skill implementations

**Detailed Technical Reference**: See [.claude/CLAUDE.md](.\.claude\CLAUDE.md) for:
- Full skill delegation patterns
- Shell tool usage (bash vs PowerShell)
- File fingerprinting for manifest tracking
- Python scripting patterns for non-ASCII character handling
- Environment setup and execution modes
