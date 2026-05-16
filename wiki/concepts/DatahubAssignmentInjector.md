---
title: "Datahub Assignment Injector"
type: concept
tags: [shipit, farm, kafka, kafka-streams, debezium, user-sync, microservice, eks]
sources:
  - "[raw/shipit/ADR16 Rework usershipper sync - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200851031/ADR16+Rework+usershipper+sync)"
  - "[raw/shipit/ShipIt-ADR2 AS UserShipper assignment synchronization into ShipIT - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200837788/ADR2+AS+User+Shipper+assignment+synchronization+into+ShipIT)"
last_updated: 2026-05-16
---

## Definition

The datahub-assignment-injector is a Kafka Streams application for ShipIT Farm that joins Attribute Store Debezium CDC topics with DataHub Debezium topics, producing merged user and shipper assignment records that are written into DataHub user management tables. It replaces the former two-component pipeline (`as-data-provider` + `shipper-assignment-injector`), resolving race conditions that occurred when shipper assignments arrived before user records were present in DataHub.

## Key Information

**Why it was created:**
- Prior design: `as-data-provider` consumed AS Kafka topics and pushed to Synchronizer; `shipper-assignment-injector` consumed DataHub and injected assignments separately
- Problem: race condition when a shipper assignment event arrived before the user record was written to DataHub; no stateful join semantics
- Solution: Kafka Streams stateful join ensures user and assignment data are correlated before writing to DataHub

**Data flow:**
- Input A: Attribute Store Debezium CDC Kafka topics (users, shipper assignments, roles, user parameters)
- Input B: DataHub Debezium Kafka topics (backend assignments)
- Processing: Kafka Streams stateful join (windowed)
- Output: DataHub user management tables: `role`, `user`, `user_backend`, `user_backend_role`, `user_shipper`, `user_parameter`; also `shipper_backend` table (auto-populated from AS data)

**What it replaces:**
- `as-data-provider`: standalone service pulling from AS Kafka topics and forwarding to Synchronizer
- `shipper-assignment-injector`: standalone service injecting shipper assignments into DataHub

**Deployment:**
- Runs on Amazon EKS (same cluster as ShipIT Farm backend)
- Reads from Kafka; writes to DataHub PostgreSQL tables

**Downstream:**
- ShipIT Synchronizer reads DataHub user management tables and applies changes to Farm backend DB
- No direct Attribute Store -> Synchronizer connection; datahub-assignment-injector is the only intermediary

## Related Links

- [[AttributeStore]] - source of all user and shipper assignment data via Kafka
- [[DataHub]] - destination; receives the joined user management records
- [[ShipItSynchronizer]] - downstream consumer of DataHub user management tables
- [[ShipItFarm]] - Farm EKS context where this component runs
- [[AmazonEKS]] - hosting platform
- [[KafkaCdc]] - Kafka infrastructure and Debezium CDC used as input
- [[DebeziumConnector]] - Debezium connectors feeding the input topics
- [[shipit-adr16-usershipper-sync-rework]] - ADR16 decision to build this component
- [[shipit-as-user-sync-adr]] - ShipIT-ADR2 formal design document
