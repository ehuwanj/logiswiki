---
title: "Routing Rule Set Envelope"
type: concept
tags: [routing, envelope, versioning, consistency, long-haul]
sources:
  - "[raw/routing/Data Modell Routing Solution - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166048419/Data+Modell+Routing+Solution)"
last_updated: 2026-05-24
---

## Definition

A Routing Rule Set Envelope (`LHRoutingRuleSetEnvelope`) is a versioned, top-level grouping of all long haul [[RoutingRuleSet]]s that must be applied together for consistent route calculation. Long haul routing is recursive across many rule sets; if the active set changes mid-calculation the result becomes incoherent. The envelope solves this by giving cache and replication a single coherent boundary.

## Key Information

**Shape:**
```json
{
  "LHRoutingRuleSetEnvelope": {
    "envelopeId": "UUID",
    "lhRuleSets": [
      {
        "ruleSetId": "UUID",
        "responsibleLocationCode": "DE210",
        "ruleSetType": "national"
      }
    ]
  }
}
```

**Versioning:**
- activeFrom (required); activeTo (optional).
- Exactly one version valid at any point in time after the first version exists.
- When creating a new rule set version `newRuleSet`:
  1. Collect all future ruleset activeFrom timestamps (including `newRuleSet.activeFrom`) into an ascending-ordered set.
  2. For each activeFrom in that set: terminate existing envelopes with activeFrom <= that activeFrom; create a new envelope referencing all rule set versions valid at that activeFrom.
- Some envelope versions may have negative validity (created then immediately invalidated) and are never visible at runtime.

**Caching:**
- Cache reset every full hour: read the envelope valid at the current full hour and the one valid at the previous full hour.
- If the active envelope changed, clear the rule set cache.
- For a single route calculation:
  - Determine reference date = current timestamp rounded down to the hour.
  - Identify the envelope valid at reference date.
  - Use only rule sets referenced by that envelope (loading from DB on cache miss).
  - Use the same envelope for the entire route calculation.

**Replication:**
- Cross-DB replication cannot guarantee that the envelope arrives only after all referenced rule sets are present.
- A background job verifies all referenced rule sets exist on target and flags the envelope as 'complete' in a non-replicated column.
- Application uses only envelopes flagged complete.
- Same mechanism handles non-transactional databases that cannot guarantee atomic publication.

## Related Links

- [[RoutingRuleSet]] - element grouped by envelope
- [[RoutingRule]] - rule abstraction
- [[RuleVersioning]] - versioning mechanism
- [[LongHaulRouting]] - context where envelopes are required
- [[RouteFinder]] - implementation
- [[data-model-routing-solution]] - source describing envelope semantics
