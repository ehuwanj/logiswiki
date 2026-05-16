---
title: "Farm Release Cycle"
type: concept
tags: [shipit, farm, release-management, branching, versioning]
sources:
  - "[raw/shipit/ADR10 Shorter Release Cycles for FARM - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200848792/ADR10+Shorter+Release+Cycles+for+FARM)"
  - "[raw/routing/Semantic Versioning Guide for Route Finder and ShipIT - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166057945/Semantic+Versioning+Guide+for+Route+Finder+and+ShipIT)"
last_updated: 2026-05-16
---

## Definition

The Farm Release Cycle is the approach for releasing new features to the ShipIT Farm faster than the classic ShipIT release cycle. Farm releases are tracked with a version format of `X.Y-fN[-rcM]` and use a dedicated `dev-farm` branch that holds only Farm-bound features, separate from the `dev` branch for classic ShipIT.

## Key Information

**Version Format:**
- `X.Y-fN[-rcM]` where X.Y is the upcoming classic version, N is the Farm release number, M is the release candidate number
- Example: `3.6-f2-rc3` = 3rd RC of 2nd Farm release for upcoming classic 3.6
- Productive releases: `3.6-f3` (no rc suffix)
- Non-productive (stage): include rc suffix

**Branch Strategy:**
- `master`: released production code only (Farm and classic)
- `dev`: upcoming classic ShipIT release changes
- `dev-farm`: upcoming Farm release changes only; based on master

**Feature Classification:**
- "Farm-first" features: backend and/or Synchronizer changes only; no DataHub or other service changes required
- Features requiring DataHub/other service changes: must wait for classic ShipIT release cycle

**DB Migration Challenge:**
- Farm SQL files use non-numeric versioning format that conflicts with Flyway's numeric ordering
- Resolved by ADR14 (uniform notation with encoded Farm version in filename)

**Context:**
- Farm is GLS-controlled infrastructure, enabling faster deployment cycles
- End goal: merge Farm and classic code into unified codebase (ADR9) while keeping faster Farm release cadence

## Related Links

- [[ShipItFarm]] - the Farm being released
- [[SemanticVersioning]] - base versioning strategy
- [[ShipIt]] - classic ShipIT with slower release cycle
- [[FlywayMigration]] - DB migration tool used by Farm
- [[shipit-adr10-farm-release-cycles]] - ADR10 source
- [[shipit-adr14-db-migration-versioning]] - DB migration versioning
- [[shipit-adr9-dev-strategy]] - codebase merge strategy
