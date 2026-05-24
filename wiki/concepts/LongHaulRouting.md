---
title: "Long Haul Routing"
type: concept
tags: [routing, long-haul, rule-based, leg-based, gls]
sources:
  - "[raw/routing/Analysis Long Haul Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166047112/Analysis+Long+Haul+Routing)"
  - "[raw/routing/Long Haul Routing - National - International - Partners - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166046940/Long+Haul+Routing+-+National+-+International+-+Partners)"
  - "[raw/routing/Migration Concept - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042960/Migration+Concept)"
  - "[raw/routing/Data Modell Routing Solution - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166048419/Data+Modell+Routing+Solution)"
last_updated: 2026-05-24
---

## Definition

Long Haul Routing is the routing of parcels between GLS (and partner) depots via linehaul trucks. It connects an Origin Location (start depot - typically from short haul routing) to a Final Location (last depot before delivery). The output includes the Outbound Sorting Flag (OSF) that is printed on the parcel label - the identifier of the last depot before the final depot.

## Key Information

**Route classification (per [[long-haul-routing-national-international-partners]]):**
- INTERNATIONAL: origin country code != final country code.
- NATIONAL: origin country code == final country code.
- These are properties of routes and route steps.

**Two implementation tracks (per [[migration-concept-routing]]):**
1. **Rule-based**: per-source-location rule sets that compute the next location. Status: core engine implemented; UniQue config auto-import in progress.
2. **Leg-based (dynamic)**: graph of tour legs + shortest-path on [[Neo4jRouting]]. Status: under evaluation; intended to replace rule-based eventually.

**Rule-based model (per [[data-model-routing-solution]]):**
- Per responsible location: one LH national rule set + one LH international rule set.
- Versioned by activeFrom / activeTo; exactly one version valid at any time.
- LH evaluation recurses from origin through intermediate locations to final.
- Access path: `SELECT * FROM longhaulruleset WHERE responsibleLocation = :sourceLocation AND nationalInd = (sourceLocation.country == finalLocation.country)`.
- Rule sets grouped into [[RoutingRuleSetEnvelope]] for consistent snapshots; cache reset hourly on envelope.

**Rule set typing:**
- Current values: `INTERNATIONAL` (created by GLS International), `NATIONAL` (created by partner, next location must belong to same partner).
- Proposed rename: `rulesetOriginator` field, values `SYSTEM`, `INTERNATIONAL_LH_MGMT`, `PARTNER_LH_MGMT`.
- Evaluation order: SYSTEM -> INTERNATIONAL_LH_MGMT -> PARTNER_LH_MGMT.

**Legacy UniQue data model (replaced):**
- TBROUTE (ROUTENO, OLOCNO, FLOCNO, ROUTEFLAG001=OSF, ACTIVEFLAG).
- TBROUTELINE (ROUTENO, ORDERNO, DEPLOCNO, ARRLOCNO).
- Routing Matrix Excel (~3,000 locations -> ~9,000,000 entries).

## Related Links

- [[ShortHaulRouting]] - paired routing phase that produces origin/final location
- [[LegBasedRouting]] - dynamic alternative implementation
- [[OutboundSortingFlag]] - LH output that goes on the label
- [[RoutingRuleSet]] - core data abstraction
- [[RoutingRule]] - individual rule
- [[RoutingRuleSetEnvelope]] - consistent snapshot abstraction
- [[Neo4jRouting]] - graph tech for leg-based
- [[TourLeg]] - leg-based data unit
- [[RouteFinder]] - implementation home
- [[UniQue]] - legacy system being replaced
- [[NemonicCode]] - LH-emitted three-digit code for Spain
- [[analysis-long-haul-routing]] - terms and analysis
- [[long-haul-routing-national-international-partners]] - ruleset typing
- [[data-model-routing-solution]] - data model + versioning
- [[migration-concept-routing]] - migration plan
- [[network-leg-based-routing]] - leg-based network bootstrap
