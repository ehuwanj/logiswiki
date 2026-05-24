---
title: "Shipper Specific Routing in Route-Finder"
type: source
tags: [routing, shipper-specific, leg-based, rule-based]
sources:
  - "[raw/routing/Shipper Specific Routing in Route-Finder - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166091817/Shipper+Specific+Routing+in+Route-Finder)"
last_updated: 2026-05-24
---

## Summary

Design analysis for Shipper Specific Routing (SSR) in Route Finder. Two business cases are considered: (1) Changing the origin location of a parcel based on shipper + final location (e.g., shipper `276a15w4KM` parcels with final location DE 131 should originate from DE 800); (2) Bundle Routing - forcing certain shipper parcels to a specific set of final locations to be routed through a designated transfer location to achieve a specific Outbound Sorting Code (OSC). The "from-to-next" rule approach has a leakage problem (works for the example DE 410 but breaks for DE 235), so a complementary leg-based approach (Neo4j subgraph) is proposed: build a per-(shipper, final-location) subgraph that excludes forbidden legs, then run shortest path.

## Key Claims

- Route Finder currently uses customerID to identify shipper; UniQue SSR uses contactID. The identifier model needs alignment.
- Business case 1 (change origin): rule input = shipperID + finalLocationCodes; rule output = originLocationCode.
- Business case 2 (bundle routing) example: shipper `276a15w4KM`, force DE 880 -> DE 870 (via OSC R87) for finals DE 470/471/650/658/...
- Naive 'from-to-next' rule (`DE 880 + finals=[...] -> next=DE 870`) breaks for unlisted finals: e.g., DE 235 routes incorrectly as DE 880 -> DE 870 -> DE 001 -> DE 235 with OSC NST.
- Leg-based fallback: build a subgraph excluding existing legs via DE 830, DE 370, DE 001 for the configured shipper + finals; then run shortest path. Forces correct DE 870 traversal.
- Maintenance burden: when a new connection (e.g., DE 370 -> DE 700) is added, the subgraph's excluded-leg list must be updated to keep the SSR intent intact.

## Evidence and Notes

- The rule JSON shape contains `ruleInput` (shipperID + finalLocationCodes) and `ruleOutput` (nextLocationCode or originLocationCode).
- Legs in the example graph: DE 880 -> {DE 870, DE 830, DE 001}; DE 800 -> {DE 870, DE 370}.
- Open question: how to identify shipper - customerID (Route Finder) vs contactID (UniQue SSR).
- The leg-based approach requires negative conditions on legs - difficult to maintain as the network grows.

## Related Links

- [[ShipperSpecificRouting]] - concept page
- [[RouteFinder]] - implementation
- [[LegBasedRouting]] - leg-based solution path
- [[Neo4jRouting]] - graph tech used for subgraph
- [[LongHaulRouting]] - context
- [[RoutingRule]] - rule structure
- [[OutboundSortingFlag]] - OSC is closely related
- [[partner-preselection]] - related "invisible-to-consumer" routing tweaks
