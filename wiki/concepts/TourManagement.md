---
title: "Tour Management (UniQue Office)"
type: concept
tags: [routing, tour-management, unique, shipit-predecessor]
sources:
  - "[raw/routing/Analysis Unique Tour Management - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166043448/Analysis+Unique+Tour+Management)"
  - "[raw/routing/Nearest zipcode match (comparison between Unique, ShipIt and RTG) - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166046677/Nearest+zipcode+match+comparison+between+Unique+ShipIt+and+RTG)"
last_updated: 2026-05-24
---

## Definition

Tour Management in UniQue is the workflow that creates and maintains delivery tours. Two paths are in use today: (1) the UniQue Office GUI plus an automated cron-driven generation/upload of tour files for select countries; (2) manual upload of tour files to an FTP server. Tour data is replicated from TBTOURM (GUI source) into TBTOUR (the table used by routing).

## Key Information

**Two configuration paths:**
- GUI: UniQue Office tour management; countries today: DE, AT, IE, PL (per TBTOURM).
- Manual FTP upload: used by all other countries.

**Automation:**
- Cron `spmd210` runs on weekdays at 18:30 to generate and upload tour files for the GUI-managed countries.
- Contact for process check: Carsten Ebert (Region Ost, D10/12).

**Tour file format example:**
- `276|3000|DE 300|01257|4711|HANNOVER||1|N|`
- Column 1 = country code (276 = DE), column 3 = location code, column 4 = postal code, etc.

**Prerequisite:**
- New locations referenced in tour files must first be set up via the MDU file process for new locations - otherwise tour file rows referencing them are rejected.

**Tables:**
- `TBTOURM` - source of GUI-managed tours; versionNo: negative = archived, 0 = current/released, positive = stored-but-not-released.
- `TBTOUR` - destination for replication; only countries with `GLSCountry='Y'` are active.
- Replication from TBTOURM (versionNo=0) -> TBTOUR (versionNo=1).

**Countries in TBTOURM:** AT, BE, DK, FR, DE, IE, MC, PL.

**Countries in TBTOUR:** 47 (broad set including AT, BE, BA, BG, HR, CY, CZ, DK, EE, FI, FR, DE, GI, GR, HK, HU, IN, IR, IE, IL, IT, XK, LV, LT, LU, MK, MT, MA, MC, ME, NL, NO, PL, PT, RO, RU, SM, RS, SK, SI, ES, SE, CH, TR, GB, VA).

**Special handling - Ireland:**
- Eircode-derived virtual codes (e.g., A63 + Ashford -> WICKASH) in TBCONVERT convtype 50236, used in TBTOUR for tour assignment.
- ShipIt creates virtual codes locally in `Postalarea` table for the same lookup.

## Related Links

- [[UniQue]] - host legacy system
- [[Eircode]] - special handling for Ireland
- [[TBZIPFORMAT]] - per-country zip format support
- [[NearestZipcodeMatch]] - lookup using tours
- [[ShortHaulRouting]] - downstream consumer
- [[RouteFinder]] - new consumer
- [[ShipIt]] - delegating consumer of UniQue routing
- [[analysis-unique-tour-management]] - source
- [[nearest-zipcode-match-comparison]] - TBTOUR usage
