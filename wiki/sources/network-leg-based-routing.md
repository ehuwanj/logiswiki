---
title: "Network for Leg Based Routing"
type: source
tags: [routing, leg-based, network, unique, isrs-13530]
sources:
  - "[raw/routing/Network for leg based routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166089735/Network+for+leg+based+routing)"
last_updated: 2026-05-24
---

## Summary

Analyzes how to bootstrap the initial network for leg-based long-haul routing from existing UniQue data (ISRS-13530). The chosen approach combines three sources: (1) International network from TBLHTOURLEG (very up-to-date per business judgment); (2) National network for Germany from the routing matrix; (3) National networks for all other countries built artificially by connecting all main locations within a country to the country's international HUB. Distances in TBLHTOURLEG were spot-checked and are largely correct, though a Here-platform bulk query is the planned workaround when bulk-distance computation is needed.

## Key Claims

- Initial network = TBLHTOURLEG international legs + DE routing matrix national + per-country artificial main-location-to-HUB connections for the rest.
- TBLHTOURLEG is considered up-to-date for international legs by business experience.
- Tour configuration types in TBLHTOURLEG:
  - One-leg tour (one direction only, ORDERNO = 0).
  - Two-leg tour (back-and-forth between two points).
  - Three-leg tour (e.g., via consolidation point - LHTourLegType = consolidation - only available for Germany).
  - Multiple daily runs between two points.
- Some three-leg tours have GB3DEL as destination - outdated EVRI (Parcelforce replacement during strikes) configuration that was never deactivated and is excluded from new routing.
- Up to 8 legs exist on some tours.
- Location identifiers in TBLHTOURLEG must be replaced by main location codes where mapping exists (via TBOBJECTREF / TBCONVERT).
- Country connections (excerpt): DE has 172 distinct international legs to 29 countries (AE, AT, BE, CA, CH, CN, CZ, DK, ES, FI, FR, GB, GR, HK, HU, IE, IN, IT, LU, MT, NL, NO, PL, RO, RU, SE, SI, TR, US); ES has 83 to 14 countries; PL has 47 to 14 countries; NL has 48; FR has 45; HU has 42; BE has 40.
- Some countries have no international legs documented in TBLHTOURLEG (require manual addition).

## Evidence and Notes

- Queries in source: full SQL for TBLHTOURLEG country breakdown, leg list, address lookup; outputs persisted in Excel files (International_Legs_TBLHTOURLEG.xlsx, Evaluation_Tour_Legs.xlsx, Legs_International.xlsx, Legs_National.xlsx, TBLHTOURLEG_Legs_Main_Location.xlsx).
- Free distance bulk-check is cost-prohibitive in TBLHTOURLEG; planned workaround: Here platform for bulk distances.
- Status snapshots: Oct 30, 2024 (country breakdown), Aug 2, 2024 (full leg list), Sep 25, 2024 (legs + main location replacement).
- For countries without international legs in TBLHTOURLEG (AD, DE need supplementation), artificial main-location-to-HUB connections fill the gap.

## Related Links

- [[LegBasedRouting]] - concept page
- [[TourLeg]] - underlying data entity (TBLHTOURLEG mapping)
- [[business-object-model-leg-based-routing]] - data model
- [[LongHaulRouting]] - parent routing concept
- [[UniQue]] - source data system
- [[MdmLocations]] - target location model
- [[RoutingMatrix]] - DE national network source
- [[migration-concept-routing]] - migration roadmap
