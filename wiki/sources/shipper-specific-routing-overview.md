---
title: "Shipper Specific Routing - Overview and Smart Routing Integration"
type: source
tags: [routing, shipper-specific, ssr, isrs-14952, gs-3704]
sources:
  - "[raw/routing/Shipper Specific Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166073393/Shipper+Specific+Routing)"
last_updated: 2026-05-24
---

## Summary

End-to-end Shipper Specific Routing (SSR) analysis and design (ISRS-14952). Today SSR is executed in ShipIt as a post-routing override: after UniQue computes a route, ShipIt looks up the customer's contactID, retrieves the configured profile, and overwrites the outbound sorting code emitted on the label. SSR is national-Germany-only today. Configuration is sourced monthly from Christian Kuhn (data supplied by Sven Hock), delivered as two JSON files to the GeoAPI team (dl-#Agile-Team-4/en02/gls), imported via the 'GLS GeoAPI - Shipper Specific Routes' API, and stored in the `cellbasedrouting` database on RDS AWS. For the new Route Finder Smart Routing, Business Case 2 (Bundle Routing) is in-scope; Business Case 1 (Change Starting Point) is open - it may remain in ShipIt or move into Short Haul.

## Key Claims

**Current state in ShipIt:**
- SSR runs AFTER UniQue routing; only the outbound sorting code is overwritten - the route itself is not changed.
- Scope: national routing in Germany only.
- Activated when configured customers print labels (ContactID-based lookup).

**Configuration files (monthly cadence):**
- `Shippers_<YYYYMMDD>.json`: assigns one `profile` to each contact via `shipperNumber` (contactID); one customer = one profile.
- `ShipperSpecificRoutes_<YYYYMMDD>.json`: defines `profile`, `targetCountry`, `targetLocation`, `outboundSortingCode`.
- Profile name composition: start region + APL/depot of customer + customer name.

**Distribution and storage:**
- Data source: Sven Hock -> Christian Kuhn -> dl-#Agile-Team-4/en02/gls (GeoAPI team).
- Imported via 'GLS GeoAPI - Shipper Specific Routes' API.
- Stored in `cellbasedrouting` DB on RDS AWS.
- Update frequency: roughly once a month; not fixed - depends on data changes. Trend: increasing (2 years ago = 4 changes/year).
- Implementation reference: ticket `GS-3704 - Call ShipperSpecificRouting microservice to capture deviating outbound sorting flag`.

**Data evaluation (2025-01-02 snapshot):**
- 66 contact IDs in `Shippers_20250102.json`; 65 valid against productive UniQue DB; `276q45FJod` does not exist.
- 698 locations in `ShipperSpecificRoutes_20250102.json`; 427 valid against productive UniQue DB.
- ~270 invalid locations recorded (DE 115..DE 899 ranges); assumption: pre-populated to cover future locations and avoid maintenance gaps.

**Business Case 1 - Change Starting Point:**
- Customer delivers parcels directly to a depot themselves; depot in OSC tells the customer where to drop the parcels.
- Example: Fuhrmann Einzelhandel GmbH (contactID `276a45eU7p`). Two depots: R46 and R50.
  - Target DE 141 / DE 142 (near Cottbus) -> OSC R46.
  - Target DE 721 / DE 725 (near Wangen) -> OSC R50.
- This is customer-facing information for the label, not actual GLS transport routing. Once parcels arrive at the RUP, downstream routing is driven by the target location (mostly Direktverkehre / direct transport).

**Business Case 2 - Bundle Routing:**
- Customer's parcels to a large target region are bundled at a designated RUP for Direktverkehre to that region.
- Example: expondo GmbH (contactID `276a15w4KM`) - bundle via RUP R80 for ~85 listed target locations (DE 470 .. DE 898).
- Advantage: heavy goods (e.g., tyres) take direct routes without detours.
- A future variant being discussed: route via specific depots based on parcel size affinity (not yet realised).

**Future state in Smart Routing - Business Case 2 implementation plan:**
1. Import process from RDS AWS `cellbasedrouting` DB into the RTG DB; the new rules must additionally determine `nextLocationCode` values (not present in the legacy ShipIt overlay).
2. Decouple SSR rules from the daily customs criteria-based rule creation; SSR rules are identified by a filled `customerId` field. The customs rule-update routine must exclude `customerId`-bearing rules. (Today the two rule sets do not overlap - international vs national, different background.)
3. Implement `outboundSortingCode` in the response, both per route step and as the route-level output for SSR and customs criteria-based rules.
4. Outbound sorting code determination logic change: if any route step already has `outboundSortingCode` set, adopt the last such value; otherwise determine as before.

**Future state - Business Case 1 status:**
- Open: may not actually concern routing - could remain in ShipIt or be modeled as part of Short Haul.

**Example rule (Bundle Routing for Viking Office customers):**
- Input: `customerId: 276a45fbOx`, `finalLocationCodes: [DE 450]` (sourceLocationCode = DE 881 inferred from context).
- Output: `nextLocationCode: DE 087`, `outboundSortingCode: R87`, `finalLocationIsNextLocation: false`.
- Without the rule the route would have been DE 881 -> DE 001 -> DE 450 with OSC `NST`.
- With the rule the route becomes DE 881 -> DE 087 -> DE 450 with OSC `R87` (set on step 1; empty on step 2).

## Evidence and Notes

- Implementation tickets aggregated under `GS-3305 Shipper Specific Routing - ShipIT - Confluence`.
- Viking Office customers identified by 10 contact IDs (276a15a0S3, 276a45fnxK, 276a15a0S8, 276a45fn3d, 276a15a0Tk, 276a15ee7N, 276a15a0Tc, 276a15ee7J, 276a45fugl, 276a45fE2U); Viking Office is in Grossostheim (postcode 63762 -> Responsible Location DE 871).
- The data evaluation lists 270+ invalid (future-proof) location codes in the ranges DE 115..DE 899.

## Related Links

- [[ShipperSpecificRouting]] - concept page (updated with current-state details from this source)
- [[RouteFinder]] - implementation home
- [[ShipIt]] - current SSR executor
- [[UniQue]] - upstream routing data source today
- [[LongHaulRouting]] - routing context
- [[ShortHaulRouting]] - Business Case 1 candidate phase
- [[OutboundSortingFlag]] - the field being overwritten today
- [[RoutingRule]] - rule structure for the future state
- [[RoutingRuleSet]] - rule set for SSR rules in Smart Routing
- [[shipper-specific-routing-route-finder]] - Route Finder design analysis (companion source)
- [[nemonic-codes-routing]] - related label-emitted code (Spain analogue)
- [[long-haul-routing-national-international-partners]] - rule set typing context
