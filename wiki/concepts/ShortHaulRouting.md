---
title: "Short Haul Routing"
type: concept
tags: [routing, short-haul, rule-based, zipcode]
sources:
  - "[raw/routing/Definition and Evaluation of Short Haul Routing Rule Sets - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166070472/Definition+and+Evaluation+of+Short+Haul+Routing+Rule+Sets)"
  - "[raw/routing/Data Modell Routing Solution - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166048419/Data+Modell+Routing+Solution)"
  - "[raw/routing/Nearest zipcode match (comparison between Unique, ShipIt and RTG) - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166046677/Nearest+zipcode+match+comparison+between+Unique+ShipIt+and+RTG)"
  - "[raw/routing/Partner Pre-selection - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166044471/Partner+Pre-selection)"
last_updated: 2026-05-24
---

## Definition

Short Haul Routing identifies the delivery tour and final location for a parcel based on the consignee address (zipcode or geoCellId) and other parcel attributes. Each depot maintains a short haul rule set covering its zipcode area; country-level default rule sets fill gaps. Short haul routing also hosts the (invisible-to-consumer) [[PartnerPreselection]] decision when partner-specific routing is required.

## Key Information

**Rule input requirement:**
- Every rule must include zipCodeId or geoCellId (or both).
- Optional other criteria allowed (service, product, customerId, consigneeType, etc.).
- Global (no-zipCode/no-geoCellId) rules are allowed only at country level - never at depot level.

**Lookup index (per [[definition-evaluation-short-haul-routing-rule-sets]]):**
- A `ZipCodeRuleSetRelation` table records (zipCodeId, ruleSetId) pairs.
- An analogous geoCellId relation exists.
- Lookup pre-filters candidate rule sets by zipCodeId or geoCellId and activity window:

```sql
SELECT rulesetid
FROM ZipCodeRuleSetRelation zrsr, ShortHaulRuleSet shrs
WHERE zrsr.zipCeId = :zipCodeId
  AND zrsr.ruleSetId = shrs.ruleSetId
  AND shrs.activefrom <= :referenceDate
  AND (shrs.activeTo IS NULL OR shrs.activeTo > :referenceDate);
```

**Outputs:** FinalLocationKey + TourKey per matching rule.

**Default zipcode handling (per [[nearest-zipcode-match-comparison]]):**
- Different per system: UniQue formats/validates zipcodes against TBZIPFORMAT; RTG uses TBZIPFORMAT.ZIPTOURLENGTH for prefix matching but does NOT validate format.
- Nearest match resolution (RTG): largest tour_zipcode <= consignee_zipcode, else smallest >= consignee_zipcode, else default routing.

**Partner pre-selection invisibility:**
- Partner pre-selection ([[PartnerPreselection]]) happens during short haul routing.
- Rules must be hidden from API consumers and from non-DevOps GLS employees.

## Related Links

- [[RuleBasedRouting]] - the routing approach underlying short haul rule evaluation
- [[LongHaulRouting]] - paired routing phase
- [[RoutingRuleSet]] - core abstraction
- [[RoutingRule]] - individual rule
- [[NearestZipcodeMatch]] - zipcode resolution
- [[TBZIPFORMAT]] - per-country zip format table
- [[PartnerPreselection]] - hidden routing tweak
- [[RouteFinder]] - implementation
- [[ShipperSpecificRouting]] - related shipper-aware routing
- [[definition-evaluation-short-haul-routing-rule-sets]] - lookup logic
- [[data-model-routing-solution]] - versioning + envelope
- [[definition-of-rules-modeling-logic]] - rule modeling examples
- [[nearest-zipcode-match-comparison]] - Unique/ShipIt/RTG zipcode behavior
- [[partner-preselection]] - partner pre-selection source
