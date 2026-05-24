---
title: "Data Model: Routing Solution"
type: source
tags: [routing, data-model, versioning, envelope, isrs-3030]
sources:
  - "[raw/routing/Data Modell Routing Solution - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166048419/Data+Modell+Routing+Solution)"
last_updated: 2026-05-24
---

## Summary

Defines the data model behind the new Route Finder routing solution (ISRS-3030). The unit of routing logic is the **routing rule set** - an ordered list of routing rules (priority-based). One depot defines three rule sets: short haul (delivery tours), long haul national, long haul international. Country-level default short haul rule sets exist as fallbacks. Rule sets are versioned by activeFrom/activeTo; for long haul, a **routing rule set envelope** groups co-versioned rule sets so that route calculation always uses a consistent snapshot. Caches and cross-database replication are coordinated through the envelope; in-flight changes use 'in-work' versions that must be released together.

## Key Claims

- One responsible location maintains a short haul ruleset + national LH ruleset + international LH ruleset.
- Country-level default short haul rule sets fill in for unconfigured zipcodes.
- A rule set is an ordered list of routing rules sorted by priority; evaluation stops at the first matching rule.
- Each rule's input must contain zipCodeId or geoCellId (or both); a rule matches if all input criteria match.
- Short haul access path: `SELECT * FROM shorthaulruleset WHERE zipcode = :zipCode AND geoCellId = :geoCellId` - see also [[definition-evaluation-short-haul-routing-rule-sets]].
- Long haul access path: `SELECT * FROM longhaulruleset WHERE responsibleLocation = :sourceLocation AND nationalInd = (sourceLocation.country == finalLocation.country)`.
- Long haul evaluation is recursive: from origin through intermediate locations until final location.
- Each ruleset has an activeFrom timestamp; activeTo is optional; exactly one version is valid at any point in time (after the first version exists).
- Version creation algorithm: insert new version with activeFrom (no activeTo); terminate prior versions with `existingVersion.activeTo = min(newVersion.activeFrom, existingVersion.activeTo)`; invalidate future versions the same way.
- Cassandra alternative: either update activeTo (querying without it) or use the highest create-timestamp version as authoritative.
- LH Routing Rule Set Envelope groups all currently valid LH rule sets together for consistent routing.
- Envelope is versioned identically to rule sets.
- Cache reset every full hour, keyed on envelope; route calculation uses the envelope valid at reference date (current timestamp rounded down to hour) for the entire calculation.
- Replication: target DB must mark envelope 'complete' only after all referenced rule sets are present; flag stored in a non-replicated column.
- 'In-work' versions support multi-rule-set atomic releases - several in-work versions sharing one activeFrom can be released together after consistency validation.

## Evidence and Notes

- Envelope JSON shape: `{ envelopeId: UUID, lhRuleSets: [{ ruleSetId, responsibleLocationCode, ruleSetType }] }`.
- ruleSetType values: national, international (matching today's terminology - see [[long-haul-routing-national-international-partners]] for proposed renaming).
- Short haul example walks through rules and inputs producing FinalLocation + Tour for four parcels.
- Versioning illustrated with diagrams in the source (iframe references in raw file).

## Related Links

- [[RoutingRuleSet]] - core abstraction
- [[RoutingRule]] - rule abstraction
- [[RoutingRuleSetEnvelope]] - consistent-snapshot abstraction
- [[RuleVersioning]] - versioning mechanism
- [[ShortHaulRouting]] - short haul context
- [[LongHaulRouting]] - long haul context
- [[definition-evaluation-short-haul-routing-rule-sets]] - short haul lookup
- [[definition-of-rules-modeling-logic]] - rule modeling examples
- [[long-haul-routing-national-international-partners]] - ruleset typing
- [[RouteFinder]] - implementation
