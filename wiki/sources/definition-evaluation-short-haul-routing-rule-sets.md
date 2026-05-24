---
title: "Definition and Evaluation of Short Haul Routing Rule Sets"
type: source
tags: [routing, short-haul, rule-evaluation, isrs-2536, isrs-2605]
sources:
  - "[raw/routing/Definition and Evaluation of Short Haul Routing Rule Sets - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166070472/Definition+and+Evaluation+of+Short+Haul+Routing+Rule+Sets)"
last_updated: 2026-05-24
---

## Summary

Specifies how short haul routing rule sets are defined and evaluated (ISRS-2536, ISRS-2605). Every rule must include zipCodeId or geoCellId (or both) so that a fast lookup of applicable rule sets is possible at routing time. A side table tracks which zipCodeId / geoCellId is used as input by which rule set; this lets the route engine pre-filter to a small set of candidate rule sets instead of evaluating every active rule set in the system. Lookups are bounded by activeFrom / activeTo per rule set version.

## Key Claims

- Every rule must have zipCodeId or geoCellId (or both) as input. Optional other criteria are allowed.
- A side relation table (`ZipCodeRuleSetRelation`) records which zipCodeId appears in which ruleSet, enabling fast lookup.
- An analogous relation is maintained for geoCellId.
- Without this pre-filter, every active rule set would have to be evaluated - prohibitively slow.
- Global rules (no zipCode/geoCellId) are allowed only at the global level (e.g., country-level defaults) - never at depot level (otherwise contradicting rules would create ambiguity).
- Default global rules cover scenarios such as a new zipcode without a depot-level rule.
- Active rule set lookup: `SELECT rulesetid FROM ZipCodeRuleSetRelation zrsr, ShortHaulRuleSet shrs WHERE zrsr.zipCeId = :zipCodeId AND zrsr.ruleSetId = shrs.ruleSetId AND shrs.activefrom <= :referenceDate AND (shrs.activeTo IS NULL OR shrs.activeTo > :referenceDate)`.

## Evidence and Notes

- Example: zipCode `DE_123` is used by rule sets A (LOC1), B (LOC1), F (LOC2), G (LOC3). At reference date these are the candidate rule sets that need to be evaluated; the others are skipped.
- Each rule set has activeFrom and optional activeTo; only one version per LocationCode is active at any time (see [[data-model-routing-solution]] for the versioning mechanism).
- The geoCellId lookup logic mirrors the zipCodeId lookup.

## Related Links

- [[ShortHaulRouting]] - concept page
- [[RoutingRuleSet]] - core abstraction being filtered
- [[RoutingRule]] - input criteria definitions
- [[RouteFinder]] - implementation
- [[data-model-routing-solution]] - versioning + envelope rules
- [[definition-of-rules-modeling-logic]] - rule input modeling
- [[nearest-zipcode-match-comparison]] - related nearest-match behavior
