---
title: "Eircode (Irish Zipcode) Handling"
type: concept
tags: [routing, zipcode, ireland, eircode, unique, shipit-predecessor]
sources:
  - "[raw/routing/Nearest zipcode match (comparison between Unique, ShipIt and RTG) - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166046677/Nearest+zipcode+match+comparison+between+Unique+ShipIt+and+RTG)"
last_updated: 2026-05-24
---

## Definition

Eircode-based routing handling is a special-case treatment for Irish addresses where a synthetic "virtual code" combines the Eircode routing key (post county) with the city/village name. Ireland is the only country where this virtual-code scheme is in use. UniQue maintains the virtual codes in TBCONVERT convtype 50236; ShipIt replicates the same data into its local `Postalarea` table.

## Key Information

**Virtual code structure:**
- 4-char abbreviation of the post county (e.g., A63 = Wicklow -> `WICK`).
- + up to 3-char abbreviation of city/village (e.g., Ashford -> `ASH`).
- => `WICKASH` for A63 / Ashford.
- For collisions (same abbreviation), suffix `AA`, `AB`, `AC`, ... is added.

**Examples (convtype 50236, A63):**
- `A63,Ashford` -> `WICKASH`.
- `A63,Greystones` -> `WICKGREAA`.
- `A63,Kilcoole` -> `WICKKILAB`.
- `A63,Kilmacanogue` -> `WICKKILAF`.
- `A63,Newtownmountkennedy` -> `WICKNEWAB`.

**Lookup paths:**
- UniQue: `SELECT convtype, convfrom, convto FROM tbconvert WHERE convtype = '50236' AND convfrom LIKE 'A63%'`.
- TBTOUR uses the virtual code (e.g., `WICKASH`) in `zipcode` column.
- ShipIt: same data, local `Postalarea` table; `PostalAreaDao.java` + `getZipCodeMappingIfApplicable` in `RoutingManager.java`.

**Validation:**
- GLS Ireland local systems have an Eircode license and validate full addresses against the Eircode list.
- UniQue currently does NOT plan such validation - it only relies on the virtual-code lookup.

## Related Links

- [[NearestZipcodeMatch]] - parent zipcode resolution
- [[ShortHaulRouting]] - host phase
- [[TourManagement]] - tour table (TBTOUR) consumes virtual codes
- [[UniQue]] - host system
- [[ShipIt]] - replication target (Postalarea)
- [[TBZIPFORMAT]] - companion per-country table
- [[nearest-zipcode-match-comparison]] - source describing this scheme
