---
title: "Shipper Specific Routing"
type: concept
tags: [routing, shipper-specific, ssr, long-haul]
sources:
  - "[raw/routing/Shipper Specific Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166073393/Shipper+Specific+Routing)"
  - "[raw/routing/Shipper Specific Routing in Route-Finder - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166091817/Shipper+Specific+Routing+in+Route-Finder)"
last_updated: 2026-05-24
---

## Definition

Shipper Specific Routing (SSR) is a routing capability that adjusts routing decisions based on the shipper identity. Today SSR runs in ShipIt as a post-routing overlay (UniQue computes a route; ShipIt overwrites the outbound sorting code for configured customers); it is national-Germany-only. The new Smart Routing integration moves SSR into Route Finder rules so that the route itself (not just the OSC) can be steered. Two business cases are recognized: (1) Change Starting Point of route (label-only OSC for self-delivered parcels); (2) Bundle Routing (force traversal of a designated transfer location for a customer's parcels to a region).

## Key Information

**Current state - ShipIt (per [[shipper-specific-routing-overview]]):**
- Runs AFTER UniQue routing as a post-processing override on the outbound sorting code.
- Route itself is unchanged.
- Scope: national Germany only.
- Trigger: configured customer prints a label.

**Configuration files (monthly cadence today):**
- `Shippers_<YYYYMMDD>.json`: assigns one `profile` per contact via `shipperNumber` (contactID). One customer = one profile.
- `ShipperSpecificRoutes_<YYYYMMDD>.json`: defines `profile`, `targetCountry`, `targetLocation`, `outboundSortingCode`.
- Profile name composition: start region + APL/depot + customer name.

**Distribution path today:**
- Data: Sven Hock -> Christian Kuhn -> GeoAPI team (dl-#Agile-Team-4/en02/gls).
- Imported via 'GLS GeoAPI - Shipper Specific Routes' API.
- Stored in `cellbasedrouting` DB on RDS AWS.
- Update cadence: roughly monthly; depends on data changes (trend: increasing).
- Implementation reference: ticket `GS-3704`.

**Identity model (legacy vs Route Finder):**
- Route Finder accepts `customerID`.
- UniQue/ShipIt SSR uses `contactID` (`shipperNumber` in config files).
- The reconciliation between these identifiers is an open question.

**Business Case 1 - Change Starting Point of route (label-only):**
- Customer delivers parcels directly to one of multiple depots; the depot is encoded in the OSC so the customer knows where to drop them.
- Example: Fuhrmann Einzelhandel GmbH (contactID `276a45eU7p`). Target DE 141 / DE 142 (Cottbus) -> OSC R46. Target DE 721 / DE 725 (Wangen) -> OSC R50.
- Once parcels reach the RUP, downstream routing is target-location-driven (mostly Direktverkehre / direct transport).
- This is customer-facing information, not actual transport routing.
- Smart Routing scoping: open - may not belong in routing at all; could remain in ShipIt or be modeled as part of [[ShortHaulRouting]].
- Earlier Route Finder design (per [[shipper-specific-routing-route-finder]]) modeled this with rule input `shipperID + finalLocationCodes` and rule output `originLocationCode`.

**Business Case 2 - Bundle Routing:**
- Customer's parcels to a large target region are bundled at a designated RUP and sent there via Direktverkehre.
- Example: expondo GmbH (contactID `276a15w4KM`) - bundle via RUP R80 for ~85 listed target locations (DE 470 .. DE 898).
- Advantage: heavy goods (tyres, etc.) take direct routes without detours.
- A future variant being discussed: route via specific depots based on parcel size affinity (not yet realised).

**Future state - Route Finder rule model for Business Case 2:**
- Example rule (Viking Office, contactID `276a45fbOx`):
  - Input: `customerId`, `finalLocationCodes: [DE 450]` (sourceLocationCode = DE 881).
  - Output: `nextLocationCode: DE 087`, `outboundSortingCode: R87`, `finalLocationIsNextLocation: false`.
- With the rule the route becomes DE 881 -> DE 087 -> DE 450 with OSC `R87` (set on step 1; empty on step 2).
- Without the rule the route would be DE 881 -> DE 001 -> DE 450 with OSC `NST`.

**Future state - Smart Routing implementation plan (Business Case 2):**
1. Import process from RDS AWS `cellbasedrouting` DB to RTG DB. SSR rules need an additional `nextLocationCode` not present in the legacy ShipIt overlay.
2. Decouple SSR rules from the daily customs criteria-based rule creation. SSR rules are identified by a filled `customerId`; the customs rule update routine must exclude `customerId`-bearing rules. (Today these two rule sets do not overlap - international vs national, different background.)
3. Implement `outboundSortingCode` field in the response - both per route step and as the route-level output - for SSR and customs criteria-based rules.
4. Outbound sorting code determination: if any route step already has `outboundSortingCode` set, adopt the last such value; otherwise determine as before.

**Naive 'from-to-next' rule pitfall (per [[shipper-specific-routing-route-finder]]):**
- A rule of the form `DE 880 + listed-finals -> next = DE 870` works for the listed finals but breaks for an unlisted final like DE 235 (which would wrongly route DE 880 -> DE 870 -> DE 001 -> DE 235 with OSC `NST`).

**Leg-based solution (per [[LegBasedRouting]] / [[Neo4jRouting]]):**
- Build a per-(shipper, finalLocation) subgraph that excludes the forbidden legs (e.g., DE 880 -> DE 830 and DE 880 -> DE 001).
- Run shortest-path on the subgraph - forces routing via DE 870.
- Maintenance burden: the excluded-leg list must be updated whenever new connections are added to the global graph.

**Data evaluation snapshot (2025-01-02 - per [[shipper-specific-routing-overview]]):**
- 66 contact IDs configured; 65 valid in UniQue; one invalid (`276q45FJod`).
- 698 locations configured; 427 valid in UniQue; ~270 invalid - assumed to be pre-populated to cover future locations.

## Related Links

- [[RouteFinder]] - implementation
- [[ShipIt]] - current SSR executor (today, overlay on top of UniQue)
- [[UniQue]] - upstream routing source today
- [[LegBasedRouting]] - chosen solution path for hard cases
- [[Neo4jRouting]] - graph technology for subgraph-based SSR
- [[LongHaulRouting]] - context
- [[ShortHaulRouting]] - candidate phase for Business Case 1
- [[RoutingRule]] - rule structure for the future state
- [[RoutingRuleSet]] - rule set for SSR rules in Smart Routing
- [[OutboundSortingFlag]] - the field SSR overrides
- [[PartnerPreselection]] - related invisible-to-consumer routing
- [[NemonicCode]] - related label-emitted code (Spain analogue)
- [[shipper-specific-routing-overview]] - end-to-end SSR analysis source
- [[shipper-specific-routing-route-finder]] - Route Finder design source
