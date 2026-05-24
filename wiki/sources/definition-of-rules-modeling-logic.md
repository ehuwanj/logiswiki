---
title: "Definition of Rules: How Logic Can Be Modelled"
type: source
tags: [routing, rule-modeling, priorities, isrs-4404]
sources:
  - "[raw/routing/Definition of Rules - How can logic be modelled in rules - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166050259/Definition+of+Rules+-+How+can+logic+be+modelled+in+rules)"
last_updated: 2026-05-24
---

## Summary

Worked example (ISRS-4404) showing how to model concrete depot-level routing decisions as rules. A Depot `GLS_DE_DE100` responsible for zipcodes DE_10001..DE_10004 defines six tour categories; three modeling styles are compared: (1) priority-based with single-valued inputs (verbose), (2) priority-based with array support for serviceCode and productCode (more compact), and (3) no priorities (requires explicit negation across all rules - explosion of rules and brittle when adding services/products). Recommended: priorities with array-valued inputs. The doc also covers Long Haul rules where the `is customs parcel` boolean and `final location` produce a `Next location`.

## Key Claims

- Without priorities, adding a service/product requires rewriting all rules - the rule-count explodes combinatorially.
- Array support on input fields (serviceCode, productCode) lets one rule cover multiple equivalent triggers.
- Sub-rule detection: rule1 (Product=BUSINESSPARCEL && Service=CASH) is fully covered by rule2 (Product=BUSINESSPARCEL); rule2 must not appear before rule1, and the system can warn on this.
- For unrelated rules (Service=CASH vs Product=BUSINESSPARCEL), priority is a business decision that the system cannot infer.
- Short haul example has six categories: CASH/EASYSTART trusted-driver tour; express parcel tour; pharma tour; per-zipcode small-parcel bike tours; van tours for two zipcode subsets.
- Long haul example shows multi-step routes (CH4002 -> DE001 -> AT059 -> HU0010 -> HU0070 for customs parcels; CH4002 -> DE001 -> HU0010 -> HU0070 for non-customs); the customs flag changes the DE001 next-location from HU0010 to AT059.

## Evidence and Notes

- Rule output for short haul: FinalLocationKey + TourKey (e.g., GLS_DE_DE101 + GLS_DE_DE101_1000).
- Rule output for long haul: Next location.
- Priority-based encoding example (compact): tour 1 = CASH+EASYSTART for all four zips; tour 2 = express in all four zips; tour 3 = pharma in all four zips; tours 4-7 = per-zip bike tours; tours 8-9 = van tours.
- Long haul rule input includes `is customs parcel` and `final location`; rule per source location.

## Related Links

- [[RoutingRule]] - rule abstraction
- [[RoutingRuleSet]] - ordered list of rules
- [[ShortHaulRouting]] - short haul context
- [[LongHaulRouting]] - long haul context
- [[data-model-routing-solution]] - versioning + envelope
- [[definition-evaluation-short-haul-routing-rule-sets]] - lookup logic
- [[long-haul-routing-national-international-partners]] - ruleset typing
