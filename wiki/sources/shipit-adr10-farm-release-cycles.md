---
title: "ShipIT ADR10: Shorter Release Cycles for FARM"
type: source
tags: [shipit, farm, release-management, branching, adr]
sources:
  - "[raw/shipit/ADR10 Shorter Release Cycles for FARM - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200848792/ADR10+Shorter+Release+Cycles+for+FARM)"
last_updated: 2026-05-16
---

## Summary

The Farm should release new features faster than the classic ShipIT cycle. Two options were explored: a dedicated FARM branch that cherry-picks features from dev, and a dual long-running branch approach (dev + dev-farm). Option 2 is described with a `dev-farm` branch based on master that holds only Farm-bound features, with a version format like `3.6-f2-rc3` (ShipIT-version-farmrelease-releasecandidate).

## Key Claims

- Farm release version format: `X.Y-fN[-rcM]` e.g., `3.6-f2-rc3` = third RC of second Farm release for upcoming classic 3.6.
- Long-running branches: `master` (released code only), `dev` (upcoming classic release), `dev-farm` (upcoming Farm release).
- Feature classification: "farm-first" features (backend/Synchronizer only, no other services) go to dev-farm; others to dev.
- "Farm-first" features cannot require changes to services like DataHub (which is part of classic ShipIT release).
- Both options noted pros/cons; DB migration versioning under dev-farm is a specific concern (see ADR14).
- Option 1 (FARM branch from dev): clear separation but requires careful management of DB change files across branches.

## Evidence and Notes

- DB migration challenge: dev-farm SQL files use non-numeric versioning; Flyway requires numeric order (addressed in ADR14).
- Release candidates only exist for non-prod (stage) environments.
- Productive Farm releases: `3.6-f3` (no rc suffix).
- This ADR is referenced by ADR14 for the DB migration versioning problem.

## Related Links

- [[ShipItFarm]] - Farm release strategy
- [[FarmReleaseCycle]] - release cycle concept
- [[shipit-adr9-dev-strategy]] - codebase merge strategy
- [[shipit-adr14-db-migration-versioning]] - DB migration versioning follow-up
