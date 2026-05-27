---
title: "Routing Rule Set"
type: concept
tags: [routing, rule-set, versioning, rule-engine]
sources:
  - "[raw/routing/Data Modell Routing Solution - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166048419/Data+Modell+Routing+Solution)"
  - "[raw/routing/Definition and Evaluation of Short Haul Routing Rule Sets - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166070472/Definition+and+Evaluation+of+Short+Haul+Routing+Rule+Sets)"
  - "[raw/routing/Long Haul Routing - National - International - Partners - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166046940/Long+Haul+Routing+-+National+-+International+-+Partners)"
  - "[raw/routing/Analysis Long Haul Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166047112/Analysis+Long+Haul+Routing)"
last_updated: 2026-05-24
---

## Definition

A Routing Rule Set is an ordered list of [[RoutingRule]]s sorted by priority. Each responsible location (depot) maintains its own rule sets: a short haul rule set (for delivery tours), a long haul national rule set, and a long haul international rule set. Country-level default short haul rule sets cover unconfigured zipcodes. Rule sets are versioned (activeFrom + optional activeTo); exactly one version is valid at any point in time per (location, type).

## Key Information

**Per-location rule sets:**
- Short haul rule set - inputs include zipCodeId / geoCellId; output is FinalLocationKey + TourKey.
- Long haul national rule set.
- Long haul international rule set.

**Country-level defaults:**
- Default short haul rule sets per country act as fallbacks.

**Versioning ([[RuleVersioning]]):**
- activeFrom timestamp (required); activeTo optional.
- New version inserted with activeFrom (no activeTo).
- Prior versions terminated: `existingVersion.activeTo = min(newVersion.activeFrom, existingVersion.activeTo)`.
- Cassandra alternative: skip activeTo update; use highest create-timestamp as authoritative.
- Active rule sets are immutable - changes create new versions.

**Long haul ruleSetType / rulesetOriginator:**
- Current: `INTERNATIONAL` (GLS International), `NATIONAL` (partner).
- Proposed: `rulesetOriginator` with values SYSTEM, INTERNATIONAL_LH_MGMT, PARTNER_LH_MGMT.
- Evaluation order: SYSTEM -> INTERNATIONAL_LH_MGMT -> PARTNER_LH_MGMT.

**Grouping into envelopes (LH):**
- All LH rule sets that should be applied together are grouped into a [[RoutingRuleSetEnvelope]] for consistent routing.

**Concurrency model:**
- 'In-work' versions allow multiple rule set changes to be staged and released atomically with one shared activeFrom.

**Short haul lookup:**
- Side table `ZipCodeRuleSetRelation` (zipCodeId, ruleSetId) for fast candidate filtering.
- Analogous geoCellId table.

## Related Links

- [[RuleBasedRouting]] - routing approach this rule set is part of
- [[RoutingRule]] - element
- [[RoutingRuleSetEnvelope]] - LH consistent snapshot
- [[RuleVersioning]] - versioning mechanism
- [[ShortHaulRouting]] - SH usage
- [[LongHaulRouting]] - LH usage
- [[RouteFinder]] - implementation
- [[data-model-routing-solution]] - data model + envelopes
- [[definition-evaluation-short-haul-routing-rule-sets]] - SH lookup
- [[long-haul-routing-national-international-partners]] - LH ruleset typing
- [[analysis-long-haul-routing]] - LH analysis
- [[definition-of-rules-modeling-logic]] - rule modeling
