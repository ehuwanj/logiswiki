---
name: query
description: Answers user questions from the local Wiki knowledge base. Invoked when the user uses the /query command or asks in natural language about content in "my notes / past decisions / previous notes / knowledge base". Must first read wiki/index.md to locate relevant pages using keyword matching and semantic similarity, then deep-read pages that explicitly match the query context, and answer with wikilink citations. Never answer from model memory. If no relevant content exists in the wiki, declare "Not found in local knowledge base — the following is a general knowledge answer" and clearly separate wiki-grounded facts from general knowledge.
user-invocable: true
---

# query Skill

## Core Objective
Transform user questions into retrieval-grounded synthesis over the local wiki. Extract evidence from relevant pages, answer with clear wikilink citations, and convert high-value outputs into persistent wiki artifacts so insights compound over time.
If the query lacks sufficient context to identify a concrete target, ask a clarification question before starting retrieval.

## Trigger Scenarios
- User types `/query <question>`
- User asks in natural language: `"What do my notes say about X"`, `"What was my past decision on Y"`, `"Look up knowledge related to Z"`
- User mentions keywords: wiki, knowledge base, notes, records

## Fallback Strategy
If the question is purely general knowledge (e.g., "how many planets are in the solar system") and wiki/index.md has no relevant content:
> Not found in local knowledge base — the following is a general knowledge answer: [direct answer]

---

## Retrieval & Synthesis Pipeline

### Step 1: Consult the Global Index
**Always the first step**: Read `wiki/index.md`

Locate entries in index.md that are relevant to the question. This is a critical step — the quality of the answer depends on finding the right pages to read. Use keyword matching, semantic understanding, and category clues to find the most relevant pages.

If the index has no plausible matches, state that explicitly before using fallback behavior.

### Step 2: Deep-Read Target Files
Select the most relevant pages found in Step 1 and use the Read tool to get their full content.

Read enough context to capture definitions, claims, caveats, dates, and conflict sections. Prefer evidence-bearing sections over short summaries.

### Step 3: Synthesize & Answer
Synthesize the information and answer the user's question.

Answer structure:
- Start with a direct answer.
- Follow with evidence and key citations.
- Include uncertainty or disagreement when pages conflict.
- If needed, add a short "What is missing" note for gaps not covered by the current wiki.

**Wikilink citation rules**:
- Whenever referencing information from a wiki page, use `[[PageName]]` in the text
- When an entire paragraph cites the same page: cite once at the start and once at the end
- For specific quoted text: use Markdown block quote `> quoted content`
- Do not cite pages that were not read in the current query workflow

### Step 4: Persist High-Value Content
If the following conditions are met, proactively ask the user whether to save as a synthesis:
- The answer is longer than 2 paragraphs
- The content is analytical, comparative, or summarizing in nature
- The answer surfaces durable insight likely to be reused

Prompt phrasing:
> This is a valuable summary — would you like me to save it to wiki/syntheses/?

After the user agrees, create the file following the CLAUDE.md spec:
```markdown
---
title: "Human-Readable Synthesis Title"
type: synthesis
tags: []
sources: [list of supporting wiki pages or raw paths]
last_updated: YYYY-MM-DD
---

# Summary Content

## Related Links
- [[ConceptName]] - related concept
```

Then:
1. Register it under the Syntheses category in `wiki/index.md`.
2. Add bidirectional links by updating relevant concept pages under `## Related Links` when appropriate.

### Step 5: Record Operation Log
Append to `wiki/log.md` **only when the query required actual retrieval** (i.e., wiki pages were read and cited). Skip logging for trivial clarifications, greetings, or queries that immediately fell back to general knowledge with no wiki pages read.

```markdown
## [YYYY-MM-DD] query | <brief description>
- **Output**: <list of referenced pages, or "instant answer, not saved">
- **Coverage**: <resolved scope and any missing areas>
```

---

## Hard Constraints
- Priority 1 - **No answering from memory**: Must search the knowledge base first
- Priority 2 - **No silent answers**: Must declare when the knowledge base has no relevant content
- Priority 3 - **No fabricated grounding**: Do not invent citations, claims, or source coverage
- Priority 4 - **No over-citation**: Cite the same page only once at the start and end of a paragraph
