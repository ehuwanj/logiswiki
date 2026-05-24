---
title: "Partner Pre-selection"
type: source
tags: [routing, partner, pre-selection, isrs-5558]
sources:
  - "[raw/routing/Partner Pre-selection - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166044471/Partner+Pre-selection)"
last_updated: 2026-05-24
---

## Summary

Analysis (ISRS-5558) of partner pre-selection: a routing feature where a specific partner is chosen for the delivery tour based on business rules that should remain invisible to the API consumer. The only attribute confirmed as a differentiator today is parcel weight (e.g., in UK all parcels with weight < 2 kg should be delivered by Royal Mail). The feature applies during short haul routing but the rules themselves must be hidden from GLS employees and partner employees alike.

## Key Claims

- Pre-selection occurs during short haul routing (i.e., during `get short haul routes`).
- The selection is invisible to the API consumer of the new routing solution.
- Weight is the only attribute agreed by business (2022-11-17 meeting) as a pre-selection differentiator.
- Example: in UK, parcels with weight < 2 kg should be delivered by Royal Mail.
- Visibility constraint: pre-selection rules must NOT be visible to all GLS employees nor to employees of GLS network partners.

## Evidence and Notes

- Open question: how to integrate this into the current routing solution while honoring the visibility constraint.
- Source links to a prior agreement meeting: "2022-11-17 External API and partner as pre-selection-condition".
- Beyond weight, other partner-pre-selection criteria are not yet in scope.

## Related Links

- [[PartnerPreselection]] - concept page
- [[ShortHaulRouting]] - routing context where pre-selection applies
- [[RouteFinder]] - implementer
- [[shipper-specific-routing-route-finder]] - related invisible-to-consumer routing
- [[RoutingRule]] - rule structure
