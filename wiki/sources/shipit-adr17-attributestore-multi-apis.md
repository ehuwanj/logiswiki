---
title: "ShipIT ADR17: Attributestore Multi APIs"
type: source
tags: [source, shipit, attribute-store, microservice, gpp, adr]
sources:
  - "[raw/shipit/ADR17 Attributestore multi APIs - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200953481/ADR17+Attributestore+multi+APIs)"
last_updated: 2026-05-16
---

## Summary

The Attribute Store currently handles both ShipIT Farm and GPP information in a single Spring Boot service sharing one database. This ADR evaluates how to separate GPP functionality into its own microservice while both services continue to access the same underlying data. Five options are presented, ranging from shared libraries to fully separate databases; no outcome is recorded in the available source.

## Key Claims

- Status: no decision recorded.
- Goal: extract GPP into its own microservice with separate deployment lifecycle.
- Option 1 (shared library for entities/repos, same DB schema): minimal DB changes; tight schema coupling; requires versioned shared library.
- Option 2 (separate DB views for GPP): quick separation; views break when base tables change; read-only enforcement possible.
- Option 3 (separate DB schemas per service): clear ownership; significant migration; cross-schema joins complex.
- Option 4 (ShipIT owns DB, GPP consumes via REST): true service autonomy; higher latency; complex distributed transactions.
- Option 5 (fully separate databases, event-driven sharing): full microservice autonomy; data duplication/sync required; eventual consistency.

## Evidence and Notes

- Current architecture: single Spring Boot service, single relational DB schema.
- Shared tables used by both GPP module and the rest of the system.
- Option 5 allows different DB technology per service (e.g., MongoDB for GPP).
- Concern: cross-service transaction management with Option 4/5.

## Related Links

- [[AttributeStore]] - the service being split
- [[ShipItFarm]] - ShipIT side of the split
