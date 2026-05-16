---
title: "Kafka CDC"
type: concept
tags: [kafka, cdc, data-sync, debezium, crossborder-plus, datahub]
sources: ["[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)"]
last_updated: 2026-05-10
---

## Definition

Kafka CDC (Change Data Capture) is the preferred approach for synchronizing Route Finder database changes and data to the ShipIt DataHub. A Debezium CDC source connector captures row-level changes from the RTG PostgreSQL database and publishes them to Kafka topics. A JDBC sink connector (owned by ShipIt team) consumes these topics and writes changes to the DataHub database.

## Key Information

- **Why Kafka over REST**: REST API-based sync was considered but deprecated due to higher implementation effort, memory/performance risks for large tables, and additional complexity in both Routing and ShipIt.
- **Preferred message format**: **Avro** - binary encoding, smaller message sizes, lower network overhead, better throughput; already used in GLS Kafka cluster. JSON is technically supported but not preferred.
- **Schema Registry**: Kafka topic key and value schemas automatically generated and maintained in the Schema Registry by Debezium. Topic key is derived from the table primary key.
- **Compacted topics**: New Kafka compacted topics are created per table to synchronize routing tables from RTG DB to DataHub DB. Compacted topics ensure the latest value per key is always retained.
- **Tables synchronized**: Tables in `routing` and `rtguniqueimport` schemas (e.g., `routing.shpartnerconfig`, `rtguniqueimport.tblocation`, `rtguniqueimport.tbtour`, etc.).
- **Allowlist control**: CDC connector configuration explicitly lists which tables and columns are synchronized. Unlisted tables/columns are not replicated, preventing premature synchronization of schema changes not yet supported by DataHub.
- **Versioned configuration**: CDC connector config is versioned per Route Finder ShipIt release, aligned with the versioned SQL migration file (schema_update_rtg_X.Y.Z.sql).
- **Ownership**: XB+ RTG team creates and manages CDC connector; ShipIt team creates and manages sink connector. Sink connector should handle unknown columns/tables gracefully.

## Related Links

- [[DebeziumConnector]] - CDC source connector used to capture RTG DB changes
- [[DataHub]] - destination of the synchronized data
- [[RouteFinder]] - source database being synchronized
- [[ShipItSynchronizer]] - downstream consumer of DataHub data
- [[Liquibase]] - manages RTG database migrations that feed the CDC pipeline
- [[route-finder-datahub-sync]] - source document with full synchronization design
