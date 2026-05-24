---
title: "Neo4j (for Leg-Based Routing)"
type: concept
tags: [routing, leg-based, neo4j, graph-database]
sources:
  - "[raw/routing/Migration Concept - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042960/Migration+Concept)"
  - "[raw/routing/Shipper Specific Routing in Route-Finder - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166091817/Shipper+Specific+Routing+in+Route-Finder)"
last_updated: 2026-05-24
---

## Definition

Neo4j is the graph database selected for the dynamic [[LegBasedRouting]] approach in Route Finder. Depots are nodes, [[TourLeg]]s are directed edges, and routes are computed via shortest-path queries. Neo4j is also used for shipper-specific routing scenarios where a custom subgraph (excluding forbidden legs) is built per (shipper, finalLocation) tuple before running shortest-path.

## Key Information

**Why Neo4j:**
- Native graph traversal is well-suited to shortest-path long haul routing.
- Supports per-query subgraphs needed by Shipper Specific Routing.

**Open evaluation items (per [[migration-concept-routing]]):**
- Performance of the shortest-path algorithm at GLS scale.
- How to express conditional leg exclusions (e.g., 'no tires through this leg').
- Data quality of LH tour legs sourced from UniQue.

**Use in Shipper Specific Routing (per [[shipper-specific-routing-route-finder]]):**
- For each shipper + final-location tuple where bundle routing is required, build a subgraph that excludes the forbidden legs.
- Run shortest-path on the subgraph.
- Negative-condition maintenance: when a new leg appears in the global graph, the subgraph's exclusion list must be updated to keep SSR intent intact.

**Status:** under evaluation; rule-based LH routing remains the primary implementation in [[RouteFinder]] today.

## Related Links

- [[LegBasedRouting]] - parent approach
- [[TourLeg]] - graph edge entity
- [[LineHaul]] - operational implementation of edges
- [[MdmLocations]] - graph node source
- [[RouteFinder]] - implementation home
- [[ShipperSpecificRouting]] - subgraph use case
- [[LongHaulRouting]] - routing context
- [[migration-concept-routing]] - migration roadmap
- [[shipper-specific-routing-route-finder]] - SSR usage
- [[network-leg-based-routing]] - network bootstrap
