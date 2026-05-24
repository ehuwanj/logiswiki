---
title: "Partner Pre-selection"
type: concept
tags: [routing, partner, pre-selection, short-haul]
sources:
  - "[raw/routing/Partner Pre-selection - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166044471/Partner+Pre-selection)"
last_updated: 2026-05-24
---

## Definition

Partner Pre-selection is a routing feature that picks a specific delivery partner based on business rules - applied during short haul routing but kept invisible to the API consumer of the new routing solution. The only attribute confirmed as a differentiator today is parcel weight (agreed by business on 2022-11-17).

## Key Information

**Where it runs:** Inside short haul routing ([[ShortHaulRouting]]).

**Visibility requirement:**
- Invisible to the API consumer.
- Not visible to all GLS employees.
- Not visible to GLS network partner employees.

**Currently confirmed differentiator:**
- Parcel weight.
- Example: in UK, parcels with weight < 2 kg should be delivered by Royal Mail.

**Open questions:**
- How to integrate the rules into the routing engine without violating the visibility constraint.
- Storage and access controls for the rules.

## Related Links

- [[ShortHaulRouting]] - host phase
- [[RouteFinder]] - implementation
- [[RoutingRule]] - rule structure
- [[ShipperSpecificRouting]] - related invisible-to-consumer routing
- [[partner-preselection]] - design source
