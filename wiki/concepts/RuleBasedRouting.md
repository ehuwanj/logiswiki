---
title: "Rule-Based Routing"
type: concept
tags: [routing, rule-engine, short-haul, long-haul]
sources:
  - "[raw/routing/Data Modell Routing Solution - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166048419/Data+Modell+Routing+Solution)"
  - "[raw/routing/Definition of Rules - How can logic be modelled in rules - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166050259/Definition+of+Rules+-+How+can+logic+be+modelled+in+rules)"
  - "[raw/routing/Definition and Evaluation of Short Haul Routing Rule Sets - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166070472/Definition+and+Evaluation+of+Short+Haul+Routing+Rule+Sets)"
  - "[raw/routing/Migration Concept - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042960/Migration+Concept)"
  - "[raw/routing/Analysis Long Haul Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166047112/Analysis+Long+Haul+Routing)"
last_updated: 2026-05-27
---

## Definition

Rule-Based Routing is the core routing approach in [[RouteFinder]] where all routing decisions are made by evaluating ordered [[RoutingRuleSet]]s. Each rule set contains prioritized [[RoutingRule]]s with input criteria and output values; evaluation stops at the first matching rule. The approach applies to both [[ShortHaulRouting]] (zipcode -> tour + final location) and [[LongHaulRouting]] (source depot -> next depot, recursively). It replaces the legacy [[UniQue]] routing model (TBROUTE/TBROUTELINE + Routing Matrix Excel).

## Key Information

**Core mechanics:**
- Rules are evaluated by ascending priority; first match wins (short-circuit).
- All input criteria within a rule are AND-ed.
- Array-valued inputs (`serviceCode: [CASH, EASYSTART]`) allow one rule to cover multiple equivalent triggers - the recommended modeling style over priority-free rules.
- Output is deterministic given a fixed set of rule sets and a reference timestamp.

**Short haul application (per [[ShortHaulRouting]]):**
- Mandatory input: zipCodeId or geoCellId.
- Optional inputs: serviceCode, productCode, customerId, consigneeType, incoterm, etc.
- Output: FinalLocationKey + TourKey.
- Lookup index: `ZipCodeRuleSetRelation` (zipCodeId, ruleSetId) pre-filters candidate rule sets before full evaluation.
- Country-level default rule sets act as fallbacks for unconfigured zipcodes.

**Long haul application (per [[LongHaulRouting]]):**
- Per responsible location: one LH national rule set + one LH international rule set.
- Inputs: final location code(s) / final country code(s), optional boolean flags (e.g., is customs parcel).
- Output: nextLocationCode; evaluation recurses from origin through intermediate depots to final.
- All LH rule sets valid at a given moment are grouped into a [[RoutingRuleSetEnvelope]] to guarantee a consistent snapshot across the recursive traversal.

**Versioning (per [[RuleVersioning]]):**
- Every [[RoutingRuleSet]] is versioned by activeFrom / activeTo.
- Active rule sets are immutable; changes produce new versions.
- Multiple staged changes can share one activeFrom for atomic release ("in-work" versions).

**Goals and status (per [[migration-concept-routing]]):**
- Goals: replace UniQue routing; support more parcel attributes (beyond zipcode) in routing decisions.
- Status: core engine implemented and proven by static import of LH Excel + Tours as rules; UniQue auto-import in progress; LH rule export back to UniQue also in progress.

**Contrast with Leg-Based Routing:**
- Rule-based requires explicit per-source-location configuration of the next depot.
- [[LegBasedRouting]] computes routes dynamically from a graph of tour legs (shortest-path on [[Neo4jRouting]]).
- Leg-based is the intended eventual replacement for LH rule-based routing; SH routing remains rule-based.

## Related Links

- [[RoutingRule]] - individual ordered rule with input criteria and output
- [[RoutingRuleSet]] - ordered list of rules per (location, type), versioned
- [[RoutingRuleSetEnvelope]] - consistent LH snapshot grouping rule sets
- [[RuleVersioning]] - activeFrom/activeTo versioning mechanism
- [[ShortHaulRouting]] - SH application of rule-based routing
- [[LongHaulRouting]] - LH application; will eventually be replaced by leg-based
- [[LegBasedRouting]] - dynamic alternative to rule-based LH routing
- [[RouteFinder]] - implementation of rule-based routing engine
- [[UniQue]] - legacy system being replaced
- [[migration-concept-routing]] - three-stream migration plan including rule-based stream
- [[data-model-routing-solution]] - data model for rule sets and envelopes
- [[definition-of-rules-modeling-logic]] - modeling guidelines and worked examples
