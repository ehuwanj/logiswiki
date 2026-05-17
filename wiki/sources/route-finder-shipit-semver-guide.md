---
title: "Semantic Versioning Guide for Route Finder and ShipIT"
type: source
tags: [routing, versioning, semver, release-management]
sources:
  - "[raw/routing/Semantic Versioning Guide for Route Finder and ShipIT - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166057945/Semantic+Versioning+Guide+for+Route+Finder+and+ShipIT)"
last_updated: 2026-05-16
---

## Summary

Route Finder and ShipIT follow Semantic Versioning 2.0.0 (MAJOR.MINOR.PATCH). ShipIT branches (e.g., `ShipIT_3.0.0`) are long-lived customer release branches created from master; bugfix branches are short-lived. Artifacts (JAR/WAR) are published to Nexus maven-releases repository and are immutable once published. The axion-release Gradle plugin automates version management.

## Key Claims

- MAJOR version: incompatible API or breaking DB schema changes (e.g., changed REST endpoint paths, mandatory column removal).
- MINOR version: backward-compatible new features (new endpoints, optional parameters).
- PATCH version: backward-compatible bug fixes, performance, security patches only.
- ShipIT branch naming: `ShipIT_3.0.0`; bugfix branch naming: `bugfix/ShipIT_3.0.0-<description>`.
- Published versions in Nexus maven-releases MUST NOT be overwritten or deleted.
- Decision rule: if any feature exists in the changeset since last release, publish minor; only if purely fixes, publish patch.
- Plugin: `pl.allegro.tech.build.axion-release` version 1.18.15 for automated version management.

## Evidence and Notes

- Version increment table: ShipIT_Version initial -> set base (3.0.0); ShipIT_Version new release -> MINOR (3.1.0); bugfix -> PATCH (3.1.0 -> 3.1.1).
- "Last tag" concept: bookmark at last release used to determine what changed.
- Example: "Fix login crash" + "Fix wrong total" = Patch; "Add export button" + "Fix typo" = Minor.
- Nexus URL: `${nexusUrl}/repository/maven-releases`.

## Related Links

- [[SemanticVersioning]] - semantic versioning concept
- [[RouteFinder]] - the Route Finder service
- [[ShipIt]] - ShipIT system
- [[route-finder-multi-shipit-version-support]] - multi-version support policy
