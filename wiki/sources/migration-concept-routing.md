---
title: "Migration Concept: Rule-Based + Partner API + Leg-Based"
type: source
tags: [routing, migration, leg-based, neo4j, rule-based]
sources:
  - "[raw/routing/Migration Concept - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042960/Migration+Concept)"
last_updated: 2026-05-24
---

## Summary

High-level migration roadmap for the new Route Finder routing engine. Three streams in progress: (1) Rule-Based Routing (core engine, with API for definition/calculation and proven by static import of LH Excel + Tours; UniQue auto-import in progress); (2) Integration of External / Partner Short Haul API (POC underway, mock API integrated, default fallback logic in place); (3) Dynamic Leg-Based Long Haul Routing on Neo4j shortest-path (intended to replace rule-based LH routing eventually). Each stream has explicit open items.

## Key Claims

- Rule-Based Routing forms the core of the new routing engine.
- Maintenance API for rule definition is available; routing API for short/long haul and combined route is available.
- Concept proven by static import of LH configuration (Excel) and Tours as routing rules.
- Automatic import of UniQue routing configuration is ongoing.
- LH routing rule export back to UniQue is also being implemented.
- Partner short haul API integration is at POC: mock API integrated; default logic kicks in when partner API is too slow.
- Dynamic Leg-Based LH Routing uses LH tour legs as a graph and finds shortest path; chosen tech is Neo4j.
- Goal of leg-based: easier administration (no need to manually define next-location per source) and plausibility checks of routes against LH traffic.
- Open: import/export to UniQue for legacy consumers; performance of leg-based algorithm; data quality of LH tour legs; ownership of LH tour leg administration; tour data import into UniQue until all systems use the new routing API.

## Evidence and Notes

- Two top-level goals of rule-based stream: replace UniQue routing; support more attributes (beyond zipcode) in routing decisions.
- Two top-level goals of leg-based stream: simplified administration; built-in route plausibility.
- Restrictions on legs by parcel attributes (e.g., tires) is an open evaluation item.

## Related Links

- [[RouteFinder]] - implementation home
- [[RoutingRuleSet]] - rule-based core abstraction
- [[LongHaulRouting]] - LH routing context
- [[ShortHaulRouting]] - SH routing context
- [[LegBasedRouting]] - dynamic alternative
- [[Neo4jRouting]] - graph tech for leg-based
- [[analysis-long-haul-routing]] - LH analysis source
- [[network-leg-based-routing]] - LH network construction
- [[business-object-model-leg-based-routing]] - leg-based data model
- [[data-model-routing-solution]] - rule-based data model
- [[analysis-unique-tour-management]] - Unique tour context
