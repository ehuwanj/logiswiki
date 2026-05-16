---
title: "ShipIT ADR9: Bringing ShipIT and Farm Development Closer Together"
type: source
tags: [source, shipit, farm, development-strategy, codebase, adr]
sources:
  - "[raw/shipit/ADR9 Bringing ShipIT and Farm development closer together - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200847081/ADR9+Bringing+ShipIT+and+Farm+development+closer+together)"
last_updated: 2026-05-16
---

## Summary

ShipIT Farm started as a fork of the fpcs repository (shipit-farm). All FPCS changes were regularly merged into shipit-farm, but this lag and merge overhead was unsustainable. The ADR evaluates how to bring the two codebases together. Decision: Option 3 - merge Farm changes back into the fpcs repository, with a multi-step validation to verify that non-Farm installations are not broken.

## Key Claims

- Decision: Option 3 - merge codebases together (Farm changes merged back to fpcs), subject to successful validation testing.
- Migration plan: (1) update Farm repo with latest FPCS, (2) build non-Farm installation, (3) test local installation, (4) test central webbackend, (5) merge if successful, (6) fallback to Option 1b if tests fail.
- Farm-specific features are designed in a non-breaking way so local/web installations should still work.
- Future: Farm (cloud-hosted) becomes the leading application; new features developed there first, then back-ported to non-Farm.
- Option 1a (shipit-farm as leading): rejected - cherry-picks between repos are complex; codebases diverge; higher merge conflict risk.
- Option 1b (fpcs as leading, status quo): rejected - same problems as current situation.
- Option 2 (shared library): rejected - refactoring shared code is likely infeasible or too complex.

## Evidence and Notes

- Original repositories: fpcs (original ShipIT) at bitbucket.gls-group.eu/projects/FPCS/repos/fpcs; shipit-farm as fork.
- Risk: Farm changes have only been tested for Farm scenarios; frontend-related code not tested.
- Scheduling system was changed for the Farm - all new scheduled jobs require review when merging.

## Related Links

- [[ShipItFarm]] - Farm codebase context
- [[ShipIt]] - original ShipIT (fpcs) codebase
- [[shipit-adr10-farm-release-cycles]] - release cycle strategy following this merge decision
