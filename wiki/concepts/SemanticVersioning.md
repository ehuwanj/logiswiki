---
title: "Semantic Versioning"
type: concept
tags: [routing, versioning, semver, release-management, shipit]
sources:
  - "[raw/routing/Semantic Versioning Guide for Route Finder and ShipIT - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166057945/Semantic+Versioning+Guide+for+Route+Finder+and+ShipIT)"
  - "[raw/routing/Support Multiple ShipIt versions in Route Finder - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166061448/Support+Multiple+ShipIt+versions+in+Route+Finder)"
last_updated: 2026-05-16
---

## Definition

Semantic Versioning (SemVer 2.0.0) is the versioning standard used for Route Finder and ShipIT releases. Versions follow `MAJOR.MINOR.PATCH` format where each component has a defined meaning. Once published to Nexus maven-releases, versions are immutable.

## Key Information

**Version Components:**
- MAJOR (X.0.0): incompatible API changes or breaking DB schema changes (changed REST paths, removed endpoints, structural DB changes)
- MINOR (0.X.0): backward-compatible new functionality (new endpoints, optional parameters, new non-breaking features)
- PATCH (0.0.X): backward-compatible bug fixes only (calculation errors, performance, security patches)

**Branch Strategy:**
- `master`: production-ready branch; all feature development merged here
- `ShipIT_X.Y.Z` branches: long-lived customer release branches cut from master
- `bugfix/ShipIT_X.Y.Z-<description>`: short-lived; merged back to ShipIT branch; increments PATCH

**Decision Rule for Patch vs Minor:**
- If any feature is present in changes since last release -> minor release
- Only if all changes are purely bug fixes -> patch release
- Applies even if fixes accompany a feature (feature presence forces minor)

**Nexus Repository:**
- Immutable releases published to `maven-releases`
- Published from master or ShipIT branch only
- Once published, version cannot be overwritten or deleted

**ShipIT Farm Version Format** (ADR10):
- `X.Y-fN[-rcM]` e.g., `3.6-f2-rc3` = 3rd RC of 2nd Farm release for upcoming classic 3.6
- Productive Farm release: `3.6-f3` (no rc suffix)

**Tool:**
- Gradle plugin: `pl.allegro.tech.build.axion-release` version 1.18.15

## Related Links

- [[RouteFinder]] - uses SemVer for release management
- [[ShipIt]] - ShipIT customer releases use SemVer
- [[ShipItFarm]] - Farm uses extended SemVer (X.Y-fN format)
- [[route-finder-shipit-semver-guide]] - versioning guide source
- [[route-finder-multi-shipit-version-support]] - multi-version support using SemVer
- [[FarmReleaseCycle]] - Farm-specific release cycle
