---
title: "ShipIT ADR11: REST OAS Design or Code First Approach"
type: source
tags: [source, shipit, farm, rest, oas, openapi, adr]
sources:
  - "[raw/shipit/ADR11 REST OAS design or code first approach - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200845870/ADR11+REST+OAS+design+or+code+first+approach)"
last_updated: 2026-05-16
---

## Summary

ShipIT uses WSDLs to generate DTOs via Apache CXF / jax-ws, which does not support arbitrary annotations. This makes it impossible to add Swagger annotations for OAS customization (JsonPropertyOrder, attribute descriptions, mandatory flags). Four options were evaluated; the decision is marked TBD as of the available source.

## Key Claims

- Status: TBD - no decision recorded in the source document.
- Problem: Apache CXF jax-ws code generation does not support arbitrary annotations like those required by Swagger.
- Option 1 (design-first OAS): full control over OAS; requires parallel maintenance of WSDL + OAS files.
- Option 2 (redesign with JAXB): use JAXB + jaxb-tools to support arbitrary annotations; keeps WSDLs as single source of truth; uncertain effort.
- Option 3 (manually edit generated OAS): no code changes; requires manual post-processing each time OAS changes.
- Option 4 (keep minimal OAS, use doxygen): no code changes; poor developer experience.
- Requirements: change OAS mandatory flags, attribute order (JsonPropertyOrder), attribute descriptions.

## Evidence and Notes

- Current setup: WSDL -> Apache CXF jax-ws -> POJOs -> OAS file (code-first).
- FARM-173: the Jira ticket driving the OAS customization requirement.
- jaxb-tools formerly known as jaxb2-annotate-plugin (GitHub: highsource/jaxb-tools).
- Swagger recommends design-first approach.

## Related Links

- [[ShipItFarm]] - Farm context
- [[ShipIt]] - ShipIT backend
