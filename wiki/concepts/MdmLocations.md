---
title: "MDM Locations"
type: concept
tags: [routing, mdm, location, kafka, master-data]
sources:
  - "[raw/routing/Business Object Model Leg Based Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166053238/Business+Object+Model+Leg+Based+Routing)"
last_updated: 2026-05-24
---

## Definition

MDM (Master Data Management) holds the new structure of GLS locations - the canonical depot/HUB master data consumed by Route Finder. MDM provides location data via a REST API (`Get Location`, `Get Location Activity`) and via Kafka topics (`Location Management (Kafka)`). Route Finder consumes the Kafka topics (rather than the REST API) due to high performance requirements and caches the data locally.

## Key Information

**Required attributes for leg-based routing:**
- `locationKey` - identifier.
- `activities` - offered services (e.g., regional distribution, delivery & pickup, sorting types).
- `geo-coordinates` - via `location.locationAddresses[MAIN_ADDRESS].longitude/latitude` (for visualization).
- `outboundSortingFlag` - emitted on labels; found in `customActivityCode` per MDM Q&A.
- `restrictions` - e.g., no tires (exact attribute path TBD).
- `activity period` - on activity level only (not on location); location is active if any activity is active.
- `locCode` - needed for route step output (mapped from legacy via [[UniQue]] keyMapping).
- `opening hours` - per activity (not yet modeled).
- `handling cost per parcel`.
- `capacity` - parcels per shift.
- `means of transport` - vans/trucks/bikes accepted at location.
- `customs handling` - central, country-specific, import vs export (planned dedicated activities).
- `countryCode` - country the location belongs to (note: LU locations physically in BE belong to LU).
- `partnerCode` - partner the location belongs to.
- `economic area`.

**Partner constraints on locations:**
- Maximum / Minimum Weight per partner.
- Max/Min Volume/Dimensions (note: parcel volume often unknown at label creation - planned for future).

**Mapping from UniQue locCode -> MDM locationKey:**
- Done via dedicated mapping service (e.g., DE 481 -> DE-480).
- Mapping created when a location/activity with legacy loccode is created.

**Q&A status (per [[business-object-model-leg-based-routing]]):**
- HUB sorting activities (import sorting, export sorting, transit sorting) will get specific activity codes.
- OutboundSortingFlag location found in `customActivityCode`.
- About 10 activity types planned; today only DP (delivery & pickup) and RD (regional distribution) exist.
- Opening hours per activity not yet available in MDM.

## Related Links

- [[LegBasedRouting]] - primary consumer
- [[TourLeg]] - references locationKey
- [[LineHaul]] - references locationKey
- [[RouteFinder]] - cache holder
- [[OutboundSortingFlag]] - sourced from MDM customActivityCode
- [[UniQue]] - legacy mapping source
- [[KafkaCdc]] - companion data sync pattern
- [[business-object-model-leg-based-routing]] - data model source
