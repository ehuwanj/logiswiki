---
title: "Database Schema Evolution"
type: concept
tags: [routing, database, schema, migration, versioning, backward-compatibility]
sources:
  - "[raw/routing/Support Multiple ShipIt versions in Route Finder - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166061448/Support+Multiple+ShipIt+versions+in+Route+Finder)"
last_updated: 2026-05-16
---

## Definition

Database Schema Evolution refers to the rules governing how the shared RTG (Route Finder) database schema may change while Route Finder supports multiple simultaneous ShipIT versions. The core principle is additive-only evolution: existing tables and columns must never be modified or removed while any dependent version remains supported.

## Key Information

**Additive-Only Rules:**
- Adding new tables: always safe; older versions simply ignore them; Kafka topic must be created for new tables
- Adding new optional columns: allowed; CDC connector and sink connector must be updated to include the new column
- Removing tables: deprecated first, documented, retained until last dependent version drops; Kafka topic then removed
- Removing optional columns: deprecated first, retained until all dependent versions drop; then CDC/sink connectors updated
- Changing column name or data type: (1) create new optional column, (2) migrate data, (3) deprecate old column; old column removed only when all dependents drop
- Changing mandatory columns, primary keys, or composite keys: (1) create new table in same schema, (2) migrate data, (3) deprecate old table

**Compatibility Verification:**
- Schema migrations run as versioned SQL scripts (`schema_update_rtg_X.Y.Z.sql`)
- Jenkins CI provisions clean Docker Compose DB, applies migration scripts in order, validates no runtime errors
- Kafka CDC connector tested with local Debezium Connect via Docker Compose
- Cucumber integration tests run against target database

**Key Constraint:**
- Because RTG DB is synchronized to DataHub via Kafka CDC, schema changes must also preserve Kafka message compatibility

## Related Links

- [[RouteFinder]] - Route Finder database being evolved
- [[DataHub]] - receives RTG data via Kafka
- [[KafkaCdc]] - CDC pipeline affected by schema changes
- [[FlywayMigration]] - Farm-side migration tool
- [[Liquibase]] - Route Finder standalone migration tool
- [[route-finder-multi-shipit-version-support]] - multi-version support policy defining these rules
- [[SemanticVersioning]] - version scheme that triggers schema migrations
