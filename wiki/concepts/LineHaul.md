---
title: "Line Haul"
type: concept
tags: [routing, leg-based, data-model, line-haul]
sources:
  - "[raw/routing/Business Object Model Leg Based Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166053238/Business+Object+Model+Leg+Based+Routing)"
last_updated: 2026-05-24
---

## Definition

A Line Haul is the operational implementation of a [[TourLeg]]: a concrete vehicle + schedule that realizes the physical transport between the two depots. A single tour leg may have multiple line haul implementations (e.g., a morning truck and an afternoon truck on the same leg).

## Key Information

**Attributes:**
- `lineHaulID`: UUID (1).
- `tourLegID`: UUID, foreign key to [[TourLeg]] (1).
- `locationKey`: String, location affiliation (1).
- `cost`: Decimal Number > 0 (Euro) (0..1).
- `vehicle type`: String, enum (1).

**Line haul departure/arrival time (separate entity):**
- `departureAndArrivalTimeID`: UUID (1).
- `lineHaulID`: UUID, ref to line haul (1).
- `departureWeekday`: MON / TUE / WED / THU / FR / SAT / SUN.
- `departureHour`: 0-23.
- `departureMinute`: 0-59.
- `arrivalWeekday`: MON / TUE / WED / THU / FR / SAT / SUN.
- `arrivalHour`: 0-23.
- `arrivalMinute`: 0-59.

**Use in routing:** A tour leg's effective cost / time on the routing graph is informed by the line haul attributes (vehicle capacity, schedule, cost).

## Related Links

- [[TourLeg]] - parent edge in the network graph
- [[LegBasedRouting]] - parent routing approach
- [[Neo4jRouting]] - graph database that stores tour leg + line haul attributes
- [[MdmLocations]] - location reference
- [[business-object-model-leg-based-routing]] - data model source
