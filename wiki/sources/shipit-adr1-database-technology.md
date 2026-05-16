---
title: "ShipIT ADR1: Database Technology"
type: source
tags: [source, shipit, farm, database, aurora, adr]
sources:
  - "[raw/shipit/ADR1 Database technology - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200844181/ADR1+Database+technology)"
last_updated: 2026-05-16
---

## Summary

This ADR selects the database technology for the ShipIT Webbackend Farm, which requires the database to be independently deployable from the backend. Four options were evaluated: self-managed PostgreSQL on EC2, Amazon RDS for PostgreSQL, Amazon Aurora, and Amazon Aurora Serverless v2. Aurora Serverless v2 with PostgreSQL compatibility was chosen based on existing positive GLS experience and its serverless ACU-based pricing model.

## Key Claims

- Decision: Option 4 - Amazon Aurora Serverless v2 with PostgreSQL-compatibility.
- Requirement: database must be PostgreSQL-compatible and independently managed from the backend EC2 instance.
- Aurora Serverless v2 scales in ACU (Aurora Capacity Units) charged per second; minimum 0.5 ACU, recommendation is at least 1 ACU to prevent connection issues.
- Aurora Serverless v2 supports PostgreSQL v11 up to v15; 3x throughput vs regular PostgreSQL per AWS.
- RDS (option 2) was already used for DataHub and was known/trusted, but Aurora Serverless v2 was preferred for auto-scaling.
- No vendor lock-in: PostgreSQL data can be exported to any other PostgreSQL database.

## Evidence and Notes

- Self-managed EC2 (option 1): no managed backups, no easy multi-AZ, high ops effort.
- RDS (option 2): no auto-scaling for read-replicas.
- Aurora (option 3): does not support up-to-date PostgreSQL versions (up to 13.4 at time of writing).
- Aurora Serverless v2 (option 4): does not support all RDS parameters; dynamic pricing is hard to predict.
- GLS already had first experience with Aurora Serverless v2 before this decision.

## Related Links

- [[ShipItFarm]] - the ShipIT Farm context
- [[AuroraServerless]] - Aurora Serverless v2 concept
- [[DataHub]] - uses RDS PostgreSQL (reference for known tech)
- [[shipit-adr2-backend-server-technology]] - backend hosting decision
