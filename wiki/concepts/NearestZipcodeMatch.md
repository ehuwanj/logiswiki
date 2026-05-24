---
title: "Nearest Zipcode Match"
type: concept
tags: [routing, zipcode, nearest-match, short-haul]
sources:
  - "[raw/routing/Nearest zipcode match (comparison between Unique, ShipIt and RTG) - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166046677/Nearest+zipcode+match+comparison+between+Unique+ShipIt+and+RTG)"
last_updated: 2026-05-24
---

## Definition

Nearest Zipcode Match is the fallback logic for identifying a tour when an exact zipcode match is not found in the tour table. The behavior differs between legacy UniQue, ShipIt (which delegates to UniQue), and the new Route Finder (RTG) - and the three approaches are being compared as part of routing migration to ensure consistent customer-facing behavior.

## Key Information

**UniQue (per [[nearest-zipcode-match-comparison]]):**
- Formats and validates zipcode against `TBZIPFORMAT` (MINZIPLENGTH, MAXZIPLENGTH, ZIPTOURLENGTH, NEARESTFLAG, ZIPFORMAT).
- Searches by zipcode in TBTOUR.
- If no tour: descending nearest match (largest ZipCode <= formatted_zip), then ascending (smallest ZipCode >= formatted_zip).
- If still no match: default zipcode + final location via TBCONVERT convtype 20365 (NEAREST) or 50607 (EXACT - currently unused).

**ShipIt:**
- No detailed zipcode validation; just strips spaces.
- UK-format validation only.
- Delegates routing to UniQue's GLRO010; consumes results via persistence layer (zipCode, PrevNearestTour, getNextNearestTour).
- Tour data accessible via `shipit.landingzone.tbtour`.

**Route Finder (RTG):**
- Does NOT format or validate the zipcode.
- Uses only the first `TBZIPFORMAT.ZIPTOURLENGTH` characters of the consignee zipcode for comparison.
- Resolves multiple matches by: largest tour_zipcode <= consignee_zipcode, else smallest tour_zipcode >= consignee_zipcode, else default routing per destination/origin country.

**Two-step UniQue example:**
- For zipcode 65761 with leitregion prefix `657`:
  - Step 1: `SELECT ... WHERE tr.ZipCode <= '65761' AND Left(tr.ZipCode, 3) = '657' ORDER BY tr.ZipCode DESC` -> take first result.
  - Step 2: `SELECT ... WHERE tr.ZipCode >= '65761' AND Left(tr.ZipCode, 3) = '657' ORDER BY tr.ZipCode ASC` -> take first result.
- For invalid prefix (e.g., 34765 where 347 is not valid), neither step finds a row; default zipcode via TBCONVERT is then determined.

## Related Links

- [[TBZIPFORMAT]] - format rules table
- [[ShortHaulRouting]] - host phase
- [[TourManagement]] - tour data source (TBTOUR)
- [[UniQue]] - legacy implementation
- [[ShipIt]] - delegating implementation
- [[RouteFinder]] - new implementation
- [[Eircode]] - Irish special-case
- [[nearest-zipcode-match-comparison]] - detailed comparison source
