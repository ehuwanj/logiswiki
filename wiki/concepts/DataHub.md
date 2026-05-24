---
title: "DataHub"
type: concept
tags: [shipit, datahub, data-sync, database, crossborder-plus, farm, kafka]
sources:
  - "[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)"
  - "[raw/shipit/ADR7 Shipper assignment automation - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200843831/ADR7+Shipper+assignment+automation)"
  - "[raw/shipit/ADR16 Rework usershipper sync - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200851031/ADR16+Rework+usershipper+sync)"
last_updated: 2026-05-16
---

## Definition

DataHub is the central data distribution database in the ShipIt ecosystem. It serves as an intermediate store between the central RTG database (in AWS) and individual customer on-premise databases. Routing data is synchronized from RTG DB -> Kafka -> DataHub DB -> ShipIt Synchronizer -> customer backend DB. For ShipIT Farm, DataHub also holds user and shipper assignment data synchronized from the Attribute Store via Kafka.

## Key Information

**Routing data pipeline (classic):**
- Route Finder (RTG) database captures changes via Debezium CDC connector
- Changes published to Kafka topics in Avro format
- ShipIt team's JDBC sink connector writes to DataHub DB
- ShipIt Synchronizer reads from DataHub DB and pushes to customer backend DB

**User/shipper data pipeline (Farm):**
- Attribute Store publishes user and shipper assignment events to Kafka topics via Debezium CDC
- `datahub-assignment-injector` (new Kafka Streams app) joins AS Kafka topics with DataHub topics and writes to DataHub DB user management tables
- Synchronizer reads user management tables and applies to ShipIT Farm backend DB
- User management tables added for Farm: `role`, `user`, `user_backend`, `user_backend_role`, `user_shipper`, `user_parameter`
- `shipper_backend` table: auto-populated from Attribute Store Kafka topic data (ADR7 Option 3); no manual maintenance required

**Schema compatibility:**
- DataHub DB schema may lag behind RTG DB schema
- CDC connector allowlist prevents newly added tables/columns from being replicated before DataHub is ready
- Sink connector should ignore unknown columns/tables (forward compatibility)
- Multi-version support: DataHub holds data for all supported ShipIt versions simultaneously

**Ownership:**
- ShipIt team owns the sink connector and Synchronizer
- XB+ RTG team owns the CDC connector and RTG database migrations
- Schema migration: XB+ RTG team delivers versioned SQL (schema_update_rtg_X.Y.Z.sql); ShipIt team executes against DataHub DB and adapts sink connector

## Related Links

- [[KafkaCdc]] - Kafka CDC pipeline that feeds DataHub
- [[DebeziumConnector]] - CDC source connector from RTG to Kafka
- [[ShipItSynchronizer]] - consumes DataHub data for customer databases
- [[RouteFinder]] - source of routing data synchronized to DataHub
- [[ShipIt]] - system owning the DataHub and Synchronizer
- [[ShipItFarm]] - Farm variant using DataHub for user/shipper sync
- [[AttributeStore]] - source of Farm user/shipper data via Kafka
- [[route-finder-datahub-sync]] - detailed data synchronization source document
- [[shipit-adr7-shipper-assignment-automation]] - shipper_backend auto-population decision
- [[shipit-adr16-usershipper-sync-rework]] - datahub-assignment-injector replacing prior components
- [[DatahubAssignmentInjector]] - Kafka Streams component that writes to DataHub user management tables
