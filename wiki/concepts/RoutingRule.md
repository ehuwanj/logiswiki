---
title: "Routing Rule"
type: concept
tags: [routing, rule, rule-engine]
sources:
  - "[raw/routing/Data Modell Routing Solution - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166048419/Data+Modell+Routing+Solution)"
  - "[raw/routing/Definition of Rules - How can logic be modelled in rules - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166050259/Definition+of+Rules+-+How+can+logic+be+modelled+in+rules)"
  - "[raw/routing/Definition and Evaluation of Short Haul Routing Rule Sets - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166070472/Definition+and+Evaluation+of+Short+Haul+Routing+Rule+Sets)"
  - "[raw/routing/Analysis Long Haul Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166047112/Analysis+Long+Haul+Routing)"
last_updated: 2026-05-24
---

## Definition

A Routing Rule is an ordered, integral part of a [[RoutingRuleSet]] with a unique priority within that rule set. It defines input criteria and output values. During evaluation, rules are checked by ascending priority; the first rule whose input criteria all match the actual input data wins, and its output values are returned.

## Key Information

**Input criteria (short haul):**
- Mandatory: zipCodeId or geoCellId (or both).
- Optional: serviceCode, productCode, customerId, consigneeType, incoterm, etc.
- Wildcards allowed in country/location lists.
- Arrays allowed (e.g., `serviceCode: [CASH, EASYSTART]`) - one rule covers multiple equivalent triggers.

**Input criteria (long haul):**
- Final location code(s) or final country code(s) (wildcards allowed).
- Additional attributes (e.g., `is customs parcel` boolean).

**Output values:**
- Short haul: `FinalLocationKey` + `TourKey`.
- Long haul: `nextLocationCode` (or `originLocationCode` for shipper-specific business case 1); may include `nextLocationIsFinalLocation` flag.

**Modeling guidelines (per [[definition-of-rules-modeling-logic]]):**
- Priority-based with array inputs is the recommended modeling style.
- Avoid priority-free rules - they require explicit negation across all rules and explode when new services/products are added.
- Sub-rule containment can be detected (rule2 fully covers rule1) and the system can warn if priorities conflict.
- For unrelated rules, priority is a business decision.

**Evaluation:**
- Stop at first match.
- All input criteria must match for a rule to apply (AND across fields).

## Related Links

- [[RoutingRuleSet]] - container
- [[RoutingRuleSetEnvelope]] - consistent snapshot grouping rule sets
- [[ShortHaulRouting]] - SH usage
- [[LongHaulRouting]] - LH usage
- [[RouteFinder]] - implementation
- [[ShipperSpecificRouting]] - shipper-aware rules
- [[PartnerPreselection]] - hidden partner rules
- [[data-model-routing-solution]] - data model
- [[definition-of-rules-modeling-logic]] - modeling examples
- [[definition-evaluation-short-haul-routing-rule-sets]] - SH lookup
