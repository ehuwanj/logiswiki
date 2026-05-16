---
title: "Route Finder DataHub Sync"
type: source
tags:
  - kafka
  - cdc
  - datahub
  - database
  - crossborder-plus
sources:
  - "[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)"
---

## Summary

This DRAFT design document (ISRS-21177, under Epic ISRS-19336) defines how Route Finder database changes and data are synchronized to the ShipIt DataHub, which then distributes data to customer on-premise databases. The preferred approach is Kafka-based Change Data Capture (CDC) using Debezium, with Avro encoding. A key design constraint is that three concurrent ShipIt versions of Route Finder must be supported on a single RTG database, requiring strict backward-compatible, additive-only schema evolution.

## Key Claims

- Route Finder uses Liquibase for DB migrations; ShipIt uses versioned SQL files (schema_update_X.Y.Z.sql). A new versioned SQL file (e.g., schema_update_rtg_X.Y.Z.sql) must be created for Route Finder DB changes.
- Preferred data sync approach: **Kafka CDC via Debezium connector** (source) → Kafka topics → JDBC sink connector → ShipIt DataHub DB → ShipIt Synchronizer → customer backend DB.
- REST API-based sync was considered but deprecated: too much implementation effort, memory/performance risks for large tables.
- Avro format preferred over JSON for Kafka messages (binary encoding, smaller size, already used in GLS Kafka cluster).
- Required DB schemas for Route Finder execution: `cron` (scheduled jobs), `routing`, `rtguniqueimport`.
- Required tables span both `routing` and `rtguniqueimport` schemas (e.g., lhroscruleset, shpartnerconfig, tbcountry, tblocation, tbtour, etc.).
- A single RTG database instance in AWS serves all supported ShipIt versions; data compatibility is enforced via CDC connector column/table allowlisting.
- Schema evolution must be additive: no column removal, no renaming, no type changes, no primary key modifications while older versions are supported.
- CDC connector configuration is versioned per Route Finder ShipIt release; each release ships with its migration SQL and CDC config.
- ShipIt Synchronizer retrieves routing data from DataHub DB filtered by its supported version's tables/columns.

## Evidence and Notes

- Two Liquibase projects: `xbp-rtg-rtg-liquibase` (routing schema) and `xbp-rtg-rtg-unique-import` (rtguniqueimport schema).
- Kafka topic key is auto-generated from table primary key by Debezium; schema registered in Schema Registry.
- Allowlist in CDC connector prevents newly added tables/columns from being replicated before DataHub schema is ready.
- XB+ RTG team creates CDC connector when a new ShipIt version build is requested; ShipIt team owns the sink connector and must adapt it for new columns/tables.
- Sink connector should ignore unknown columns/tables (forward compatibility).
- Database change rules summary: new tables/columns are allowed; dropping/renaming/type changes require deprecation-first and only after all supported versions are migrated off.
- Grace period before column/table removal: until the oldest supported ShipIt version (e.g., shipit-3.x) is no longer supported.

## Related Links

- [[DataHub]] - ShipIt DataHub for data distribution
- [[KafkaCdc]] - Kafka CDC synchronization pattern
- [[DebeziumConnector]] - Debezium CDC source connector
- [[Liquibase]] - Route Finder database migration framework
- [[ShipIt]] - ShipIt on-premise system
- [[RouteFinder]] - Route Finder (RTG) component
- [[shipit-route-finder-deployment-comparison]] - deployment model for Route Finder with ShipIt
