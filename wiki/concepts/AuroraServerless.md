---
title: "Aurora Serverless"
type: concept
tags: [aws, aurora, database, postgresql, serverless]
sources:
  - "[raw/shipit/ADR1 Database technology - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200844181/ADR1+Database+technology)"
last_updated: 2026-05-16
---

## Definition

Amazon Aurora Serverless v2 with PostgreSQL compatibility is AWS's managed database service that automatically scales based on load using Aurora Capacity Units (ACUs) charged per second. It provides the storage architecture of Aurora (6 copies across 3 AZs) with on-demand compute scaling. GLS chose it for ShipIT Farm database (ADR1) based on existing positive experience.

## Key Information

- Scales in ACUs (Aurora Capacity Units); minimum 0.5 ACU but at least 1 ACU recommended to prevent connection issues
- Supports PostgreSQL v11 to v15 (at time of ADR1)
- AWS claims 3x throughput vs standard PostgreSQL; 6 storage copies across 3 AZs
- No scale-to-zero; at minimum 0.5 ACU must be provisioned
- Faster crash recovery compared to RDS or regular PostgreSQL clusters
- Does not support all RDS parameters; some platform-specific limitations
- Pricing is usage-based (per ACU-second); harder to predict costs vs fixed EC2/RDS
- No vendor lock-in: PostgreSQL data exportable to any PostgreSQL database

**Comparison vs. Amazon RDS:**
- RDS: known tech at GLS (used for DataHub), no read-replica auto-scaling
- Aurora Serverless v2: faster auto-scaling, fully managed compute/storage separation, serverless pricing

## Related Links

- [[ShipItFarm]] - uses Aurora Serverless v2 as its database
- [[DataHub]] - uses Amazon RDS PostgreSQL (reference)
- [[shipit-adr1-database-technology]] - ADR choosing Aurora Serverless v2
