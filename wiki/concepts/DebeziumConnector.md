---
title: "Debezium Connector"
type: concept
tags: [kafka, debezium, cdc, data-sync, postgresql, crossborder-plus]
sources:
  - "[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)"
last_updated: 2026-05-17
---

## Definition

Debezium is an open-source Change Data Capture (CDC) connector that monitors database transaction logs and publishes row-level change events to Kafka topics. In the CrossBorder+ Route Finder synchronization architecture, a Debezium source connector captures changes from the RTG PostgreSQL database and publishes them to the GLS Kafka cluster.

## Key Information

- **Role**: Debezium is the CDC source connector in the RTG→DataHub synchronization pipeline. It replaces the need for a polling-based or REST-based synchronization approach.
- **How it works**: Reads PostgreSQL WAL (write-ahead log) to detect INSERT/UPDATE/DELETE events; publishes each change as a Kafka message with the table primary key as the message key.
- **Topic keys**: Automatically generated from table primary keys; schema registered in Schema Registry.
- **Initial load**: Debezium connector loads historical data from the source table upon first start, then streams ongoing changes.
- **Allowlist configuration**: Debezium connector config explicitly specifies which tables and columns to capture. Unlisted objects are ignored - this prevents premature replication of schema changes not yet supported by DataHub.
- **Versioned configuration**: CDC connector configuration is versioned with each Route Finder ShipIt release (aligned with schema_update_rtg_X.Y.Z.sql).
- **Ownership**: Created and managed by the XB+ RTG team. A new connector is created when a new ShipIt version build is requested.
- **Format**: Produces Avro-encoded messages (preferred; binary, compact, already used in GLS Kafka).

## Related Links

- [[KafkaCdc]] - broader Kafka CDC synchronization pipeline
- [[DataHub]] - destination of the Debezium-captured changes
- [[RouteFinder]] - source PostgreSQL database being monitored
- [[Liquibase]] - manages the schema of the RTG database that Debezium reads
- [[route-finder-datahub-sync]] - source document with full connector design
