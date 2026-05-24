---
title: "UniQue (Legacy GLS System)"
type: concept
tags: [routing, unique, legacy, shipit-predecessor]
sources:
  - "[raw/routing/Analysis Long Haul Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166047112/Analysis+Long+Haul+Routing)"
  - "[raw/routing/Analysis Unique Tour Management - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166043448/Analysis+Unique+Tour+Management)"
  - "[raw/routing/Network for leg based routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166089735/Network+for+leg+based+routing)"
  - "[raw/routing/Nearest zipcode match (comparison between Unique, ShipIt and RTG) - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166046677/Nearest+zipcode+match+comparison+between+Unique+ShipIt+and+RTG)"
last_updated: 2026-05-24
---

## Definition

UniQue is the GLS legacy backbone system whose routing logic, tour management, and location master data the new Route Finder and ShipIt stack are progressively replacing. UniQue contains the canonical tour and route data the migration team uses to bootstrap the new rule-based and leg-based routing engines.

## Key Information

**Tables relevant to routing:**
- `TBROUTE` - long haul routes (ROUTENO, OLOCNO, FLOCNO, ROUTEFLAG001=OSF, ACTIVEFLAG).
- `TBROUTELINE` - long haul route steps (ROUTENO, ORDERNO, DEPLOCNO, ARRLOCNO).
- `TBLHTOURLEG` - long haul tour legs (international network seeded from here).
- `TBLHTOUR` - long haul tours (parent of TBLHTOURLEG).
- `TBLOCATION` - location master (LOCCODE, LOCTYPE, COUNTRYNUM, etc.).
- `TBTOUR` - tours used by GLS (replication target of TBTOURM).
- `TBTOURM` - tours maintained via GUI (countries: AT, BE, DK, FR, DE, IE, MC, PL).
- `TBZIPFORMAT` - per-country zipcode format rules.
- `TBCONVERT` - mappings, including locCode-to-mainLocation, defaults (convtype 20365/50607), Eircode virtual codes (convtype 50236).
- `TBOBJECTREF` - additional reference mappings (incl. main-location mappings).
- `TBSYSVALUE` - enum/sysvalue lookups (incl. LH tour leg types).
- `TBADDRESS`, `TBCONTACT`, `TBOBJADDRESS`, `TBCOUNTRY` - addresses and country master.

**Routing entry points:**
- `GLRO010` (`calculateRouting`) - main routing call.
- `GLRO010A` - tour identification.
- `GLRO01B` - zipcode formatting/validation.
- `GLRO018B` - UK-specific zipcode formatting.
- Express path: `calculateExpressRouting` (no nearest zipcode match).

**Tour Management:**
- GUI "Tour Management" replicates TBTOURM (versionNo=0 -> versionNo=1 in TBTOUR).
- Cron `spmd210` runs at 18:30 weekdays for DE/AT/IE/PL.
- Manual FTP upload also supported.
- See [[TourManagement]].

**Replacement strategy:**
- LH routing rules: replaced by [[RoutingRuleSet]] in [[RouteFinder]].
- LH tour legs: feeding [[LegBasedRouting]] in Neo4j.
- Tour management: still being designed for the new platform.
- Default zipcode lookup (convtype 20365/50607): replaced by new logic in Route Finder.

## Related Links

- [[RouteFinder]] - new replacement
- [[ShipIt]] - shipping system that historically delegated routing to UniQue
- [[LongHaulRouting]] - what UniQue's TBROUTE/TBROUTELINE encoded
- [[LegBasedRouting]] - what TBLHTOURLEG seeds
- [[TourManagement]] - tour management in UniQue
- [[RoutingMatrix]] - the Excel input behind TBROUTE
- [[TBZIPFORMAT]] - zipcode format table
- [[NearestZipcodeMatch]] - zipcode resolution
- [[Eircode]] - Irish virtual codes in TBCONVERT
- [[analysis-long-haul-routing]] - LH analysis
- [[analysis-unique-tour-management]] - tour management analysis
- [[network-leg-based-routing]] - leg-based bootstrap from UniQue
- [[nearest-zipcode-match-comparison]] - zipcode behavior comparison
