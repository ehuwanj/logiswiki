---
title: "Analysis: Long Haul Routing"
type: source
tags: [routing, long-haul, rule-based, unique]
sources:
  - "[raw/routing/Analysis Long Haul Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166047112/Analysis+Long+Haul+Routing)"
last_updated: 2026-05-24
---

## Summary

Definitional analysis of long haul routing concepts and the legacy UniQue data model (TBROUTE / TBROUTELINE) versus the new rule-based approach. Long Haul Routing is the routing between GLS (and partner) depots via linehaul trucks; the output includes the Outbound Sorting Flag (OSF) that is printed on the parcel label. The new approach replaces UniQue's large routing Excel ('Routing Matrix') - which contains ~9,000,000 (sourcelocation, finallocation) entries for ~3,000 locations - with versioned, ordered rule sets that compute the next location for each source location.

## Key Claims

- Long Haul Routing covers depot-to-depot transport (legacy: linehaul trucks).
- Origin Location = start depot; Final Location = last depot before delivery (from short haul routing).
- Outbound Sorting Flag (OSF) / Outbound Sorting Information = identifier of the last depot before the final depot printed on the label; corresponds to the TSPCode of the sorting location.
- Even though a parcel can pass several sorting hubs, only the OSF for the last hub is returned (label space constraint).
- Legacy UniQue table TBROUTE columns: ROUTENO, OLOCNO (origin), FLOCNO (final), ROUTEFLAG001 (OSF), ACTIVEFLAG.
- Legacy UniQue table TBROUTELINE columns: ROUTENO, ORDERNO, DEPLOCNO (departure), ARRLOCNO (arrival).
- Routing Matrix Excel contains for each (sourcelocation, finallocation) pair the next location; ~3,000 locations -> ~9M entries.
- New approach: per-source-location route-rule-set; rule set is versioned (activeFrom + optional activeTo); only one version is valid at any point in time.
- Active route-rule-sets are immutable; updates create new versions.
- Rule input: list of final location codes or list of final country codes; wildcards allowed; rule matches if any pattern matches.
- Future criteria envisioned: dedicated routings for specific customers, tires, etc.

## Evidence and Notes

- Rule definition idea per source location:
  - List directly-delivered destinations -> set `nextLocationIsFinalLocation` flag.
  - List connected HUBs and their served final locations -> a rule per HUB.
  - List RUPs used for a particular final country -> country-level rules.
  - Final fallback rule = default routing.
  - Wildcards require that exception rules be placed before catch-all rules.
- Example pending: parcel from Hannover (DE 300) to Brunnenbachstrasse 21, 86157 Augsburg (TODO from raw source).

## Related Links

- [[LongHaulRouting]] - concept page
- [[OutboundSortingFlag]] - OSF concept
- [[RouteFinder]] - implementation of new routing
- [[UniQue]] - legacy system being replaced
- [[RoutingRuleSet]] - new versioned rule set abstraction
- [[RoutingRule]] - rule abstraction
- [[RoutingMatrix]] - legacy Excel routing source
- [[long-haul-routing-national-international-partners]] - scope and ruleset ordering
- [[network-leg-based-routing]] - leg-based alternative to rule-based
- [[data-model-routing-solution]] - data model behind the new approach
- [[migration-concept-routing]] - migration roadmap
