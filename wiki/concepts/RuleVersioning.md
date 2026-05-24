---
title: "Rule Versioning (Routing)"
type: concept
tags: [routing, versioning, activefrom, activeto]
sources:
  - "[raw/routing/Data Modell Routing Solution - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166048419/Data+Modell+Routing+Solution)"
  - "[raw/routing/Analysis Long Haul Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166047112/Analysis+Long+Haul+Routing)"
last_updated: 2026-05-24
---

## Definition

Rule Versioning is the activeFrom/activeTo mechanism used by [[RoutingRuleSet]] and [[RoutingRuleSetEnvelope]] in the new Route Finder. Each version has an activeFrom timestamp (required) and an optional activeTo. After the first version exists, exactly one version is valid at any point in time. Active versions are immutable; changes are persisted as new versions with a new activeFrom.

## Key Information

**Creation algorithm:**
1. Insert the new version with activeFrom, leaving activeTo NULL.
2. For all prior versions, set: `existingVersion.activeTo = min(newVersion.activeFrom, existingVersion.activeTo)`.
3. For all future versions, do the same: `existingVersion.activeTo = min(newVersion.activeFrom, existingVersion.activeTo)`.

This ensures versions chained linearly with no overlaps or gaps after the first version is in place.

**Cassandra-compatible variant:**
- Cassandra cannot efficiently filter by activeTo if it gets updated. Two options:
  - Variant A: update activeTo as above; to find the valid version at refDate, read all versions with activeTo <= refDate and pick `refDate < activeTo` in code.
  - Variant B: do NOT update activeTo; pick the version with the highest create timestamp from versions with activeTo <= refDate.

**Immutability invariant:**
- Active rule sets / envelopes are NEVER modified in place.
- All changes produce new versions; old versions are terminated, not edited.

**In-work versions (atomic multi-rule release):**
- Several rule set changes can be batched as 'in-work' versions stored in DB but not used by the route engine.
- All in-work versions in a batch must share the same activeFrom.
- On release: consistency validated, rule set versions + envelopes are created/updated in one logical transaction.

## Related Links

- [[RoutingRuleSet]] - versioned entity
- [[RoutingRuleSetEnvelope]] - versioned LH grouping
- [[RoutingRule]] - rule within a versioned rule set
- [[LongHaulRouting]] - primary user
- [[ShortHaulRouting]] - also uses versioning
- [[data-model-routing-solution]] - source
