---
title: "TBZIPFORMAT (UniQue Zipcode Format Table)"
type: concept
tags: [routing, zipcode, unique, format, master-data]
sources:
  - "[raw/routing/Nearest zipcode match (comparison between Unique, ShipIt and RTG) - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166046677/Nearest+zipcode+match+comparison+between+Unique+ShipIt+and+RTG)"
last_updated: 2026-05-24
---

## Definition

`TBZIPFORMAT` is the UniQue table that holds per-country zipcode formatting rules used by routing. Each row defines how to validate and normalize a zipcode before tour lookup, and how many of its leading characters are significant for nearest-match resolution.

## Key Information

**Columns:**
- `MINZIPLENGTH`, `MAXZIPLENGTH` - acceptable length bounds.
- `ZIPTOURLENGTH` - number of leading characters used for matching against tour zipcodes.
- `NEARESTFLAG` - whether nearest match is enabled.
- `ZIPFORMAT` - format string per character.

**ZIPFORMAT character grammar:**
- `$` = letter required.
- `#` = digit required.
- `!` = any character required.
- Any other character is literal (e.g., `00-000` for Polish format).
- Exception: `*UK*` triggers UK-specific formatting (handled in GLRO018B).

**Usage:**
- UniQue (GLRO010/GLRO01B): validates and normalizes consignee zipcode per row before looking up tours; rejects malformed values.
- ShipIt: does not consume this directly - delegates routing entirely to UniQue.
- Route Finder (RTG): uses only `ZIPTOURLENGTH` (no format validation); applies it as the prefix length for nearest-match.

**UK example (`*UK*`):**
- Input: `cb22 4rg`.
- Step 1: uppercase -> `CB22 4RG`.
- Step 2: strip blanks -> `CB224RG`.
- Step 3: locate last digit (here, the `4`).
- Step 4: insert blank before last digit -> `CB22 4RG`.

**Default fallbacks (when no tour matches a valid zipcode):**
- TBCONVERT convtype 20365 (NEAREST) - in use.
- TBCONVERT convtype 50607 (EXACT) - new code introduced for US, no entries today.

## Related Links

- [[NearestZipcodeMatch]] - primary consumer
- [[ShortHaulRouting]] - host phase
- [[UniQue]] - host system
- [[RouteFinder]] - new consumer (uses ZIPTOURLENGTH only)
- [[ShipIt]] - delegating consumer
- [[Eircode]] - special case
- [[nearest-zipcode-match-comparison]] - source describing the table
