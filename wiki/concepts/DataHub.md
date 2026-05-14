---
title: "DataHub"
type: concept
tags: [shipit, datahub, data-sync, database, crossborder-plus]
sources: ["[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)"]
last_updated: 2026-05-10
---

## Definition

DataHub is the central data distribution database in the ShipIt ecosystem. It serves as an intermediate store between the central RTG database (in AWS) and individual customer on-premise databases. Routing data is synchronized from RTG DB → Kafka → DataHub DB → ShipIt Synchronizer → customer backend DB.

## Key Information

- **Role**: Acts as a hub between the centrally managed RTG database and the distributed on-premise ShipIt installations. Decouples RTG schema evolution from customer database state.
- **Synchronization flow**:
  1. Route Finder (RTG) database in AWS captures changes via CDC connector (Debezium)
  2. Changes published to Kafka topics in Avro format
  3. ShipIt team's JDBC sink connector consumes Kafka topics and writes to DataHub DB
  4. ShipIt Synchronizer reads from DataHub DB and pushes to customer backend DB
- **Schema compatibility**: DataHub DB schema may lag behind RTG DB schema. CDC connector allowlist prevents newly added tables/columns from being replicated before DataHub is ready. Sink connector should ignore unknown columns/tables (forward compatibility).
- **Multi-version support**: DataHub handles data for all supported ShipIt versions simultaneously. Each ShipIt version's Synchronizer reads only the tables/columns compatible with its supported Route Finder version.
- **Ownership**: ShipIt team owns the sink connector and Synchronizer. XB+ RTG team owns the CDC connector and RTG database migrations.
- **Schema migration process**: XB+ RTG team delivers versioned migration SQL (schema_update_rtg_X.Y.Z.sql); ShipIt team executes it against DataHub DB; ShipIt team adapts sink connector for new tables/columns.

## Related Links

- [[KafkaCdc]] — Kafka CDC pipeline that feeds DataHub
- [[DebeziumConnector]] — CDC source connector from RTG to Kafka
- [[ShipItSynchronizer]] — consumes DataHub data for customer databases
- [[RouteFinder]] — source of routing data synchronized to DataHub
- [[ShipIt]] — system owning the DataHub and Synchronizer
- [[route-finder-datahub-sync]] — detailed data synchronization source document
