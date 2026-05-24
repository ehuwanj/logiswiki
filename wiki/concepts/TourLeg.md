---
title: "Tour Leg"
type: concept
tags: [routing, leg-based, data-model, tour-leg]
sources:
  - "[raw/routing/Business Object Model Leg Based Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166053238/Business+Object+Model+Leg+Based+Routing)"
  - "[raw/routing/Network for leg based routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166089735/Network+for+leg+based+routing)"
last_updated: 2026-05-24
---

## Definition

A Tour Leg is the directed-edge unit in the [[LegBasedRouting]] network: a connection from one depot ([[MdmLocations]]) to another with basic attributes (distance, internationalFlag, validity window). One tour leg is implemented by one or more [[LineHaul]] elements that carry the operational details (vehicle type, cost, schedule).

## Key Information

**Attributes:**
- `tourLegID`: UUID, internal identifier.
- `startLocationKey`: String, start depot (1).
- `endLocationKey`: String, end depot (1).
- `distance`: Number > 0 (km).
- `internationalFlag`: Boolean.
- `validFrom`: Timestamp.
- `validTo`: Timestamp (0..1).
- `insertUserID`, `insertTimestamp` (1 each).
- `updateUserID`, `updateTimestamp` (0..1 each).

**Origin in UniQue:**
- Stored in `TBLHTOURLEG` (long haul tour leg table).
- LH tour types: one-leg (one direction, ORDERNO=0), two-leg (back-and-forth), three-leg (e.g., via consolidation point - DE only), multiple daily runs.
- Distances are spot-checked correct; bulk distance verification is cost-prohibitive in UniQue (planned workaround: Here platform).
- `LHTourLegType = consolidation` only exists in Germany.

**Special cases:**
- Some three-leg tours to GB3DEL (EVRI replacement for Parcelforce during strikes) are outdated and excluded from new routing.
- Location identifiers in TBLHTOURLEG sometimes need to be replaced by their main-location codes via TBOBJECTREF / TBCONVERT mapping.
- Up to 8 legs exist on some tours.

**International network bootstrap:** see [[network-leg-based-routing]] for the SQL that extracts active international legs from TBLHTOURLEG.

## Related Links

- [[LineHaul]] - operational implementation of a tour leg
- [[LegBasedRouting]] - parent routing approach
- [[Neo4jRouting]] - graph database storing tour legs
- [[MdmLocations]] - endpoint depots
- [[UniQue]] - legacy source (TBLHTOURLEG)
- [[business-object-model-leg-based-routing]] - data model source
- [[network-leg-based-routing]] - network bootstrap source
- [[LongHaulRouting]] - usage context
