---
title: "Routing Matrix (Legacy UniQue Excel)"
type: concept
tags: [routing, legacy, routing-matrix, unique]
sources:
  - "[raw/routing/Analysis Long Haul Routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166047112/Analysis+Long+Haul+Routing)"
  - "[raw/routing/Network for leg based routing - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166089735/Network+for+leg+based+routing)"
last_updated: 2026-05-24
---

## Definition

The Routing Matrix is the legacy Excel file in UniQue (example: `Routing Matrix V03-2288_2022-09-12_13-30-51.xlsm`) that maintains the long haul routing for GLS. For each (source location, final location) pair, it defines the next location toward the final destination. With ~3,000 locations, this matrix contains roughly 9,000,000 entries (with substantial duplication). UniQue derives the TBROUTE and TBROUTELINE table contents from this Excel.

## Key Information

- File pattern: `Routing Matrix V<version>_<date>_<time>.xlsm`.
- Maintains: for each (sourcelocation, finallocation) pair, the next location.
- Locations covered: ~3,000.
- Entries: ~9,000,000 (~3,000 squared).
- Output: feeds TBROUTE (route header) and TBROUTELINE (ordered route steps).
- Used to seed: the DE national network for [[LegBasedRouting]] (per [[network-leg-based-routing]]).
- Replacement: the new rule-based approach (rule sets with wildcards) eliminates the duplication and combinatorial size.

## Related Links

- [[UniQue]] - host system
- [[LongHaulRouting]] - what the matrix encodes
- [[LegBasedRouting]] - the new approach using TBLHTOURLEG + this matrix for DE national
- [[RoutingRuleSet]] - new rule-based replacement
- [[RouteFinder]] - eventual replacement consumer
- [[analysis-long-haul-routing]] - explains the matrix size
- [[network-leg-based-routing]] - bootstrap source
- [[migration-concept-routing]] - import status
