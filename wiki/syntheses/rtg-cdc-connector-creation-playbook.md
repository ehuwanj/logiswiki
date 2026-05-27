---
title: "RTG CDC Connector Creation Playbook"
type: synthesis
tags: [routing, kafka, cdc, debezium, datahub, route-finder, crossborder-plus, playbook]
sources:
  - "[[route-finder-datahub-sync]]"
  - "[[route-finder-multi-shipit-version-support]]"
  - "[[KafkaCdc]]"
  - "[[DebeziumConnector]]"
  - "[[DatabaseSchemaEvolution]]"
  - "[[RouteFinder]]"
  - "[[Liquibase]]"
last_updated: 2026-05-24
---

## Purpose

Step-by-step checklist for the XB+ RTG team to create a new CDC connector that synchronizes the latest RTG (Route Finder) PostgreSQL database to the GLS Kafka cluster, feeding the ShipIt [[DataHub]] sink and ultimately the on-premise customer backends.

Target architecture (per [[route-finder-datahub-sync]] and [[KafkaCdc]]):

```
RTG PostgreSQL  ->  Debezium source connector  ->  Kafka (Avro, compacted topics, Schema Registry)
                                                     |
                                                     v
                                      JDBC sink connector (ShipIt team)
                                                     |
                                                     v
                                               ShipIt DataHub DB
                                                     |
                                                     v
                                          ShipIt Synchronizer
                                                     |
                                                     v
                                       Customer on-premise backend DB
```

## Step 1: Confirm scope and target ShipIT version

- Identify the ShipIT release this connector is being created for. A new CDC connector is provisioned when a new ShipIt version build is requested, and the connector configuration is versioned per Route Finder ShipIt release ([[DebeziumConnector]], [[KafkaCdc]]).
- Confirm the support window: Route Finder must support the three most recent ShipIT versions concurrently on one shared RTG DB in AWS ([[route-finder-multi-shipit-version-support]]).

## Step 2: Prepare the RTG database side

From [[RouteFinder]] and [[route-finder-datahub-sync]]:

- Ensure the three required schemas exist: `routing`, `rtguniqueimport`, `cron`.
- Apply DB changes via [[Liquibase]] in the unified project `xbp-rtg-rtg-liquibase`, which covers both the `routing` and `rtguniqueimport` schemas (the formerly separate `xbp-rtg-rtg-unique-import` project was merged into `xbp-rtg-rtg-liquibase` under ticket ISRS-24217).
- Produce the versioned ShipIt migration file `schema_update_rtg_X.Y.Z.sql` for this release. This is what ShipIt's Component Updater consumes alongside the connector config.
- Enable PostgreSQL logical replication prerequisites so Debezium can read the WAL: `wal_level=logical`, replication slot, publication, replication user. The [[DebeziumConnector]] page confirms it works by reading the PostgreSQL WAL for INSERT/UPDATE/DELETE events.

## Step 3: Enforce additive-only schema rules

This is the load-bearing constraint - violating it breaks older ShipIT versions still consuming the same Kafka stream ([[DatabaseSchemaEvolution]]):

- New tables: allowed; a new Kafka topic must be created.
- New optional columns: allowed; CDC connector and sink connector must both be updated to include them.
- Removing tables/columns: deprecate first, retain until the last dependent ShipIt version (e.g., shipit-3.x) is dropped, only then remove the topic/column.
- Renames or type changes: forbidden in place - create new column/table, migrate data, deprecate old, remove later.
- Mandatory column / PK / composite key changes: create a new table in the same schema, migrate, deprecate the old.

## Step 4: Author the Debezium source connector configuration

Per [[DebeziumConnector]] and [[KafkaCdc]]:

- **Connector type:** Debezium PostgreSQL source connector.
- **Allowlist** the exact tables and columns to be captured. Tables span both `routing` and `rtguniqueimport` (e.g., `routing.shpartnerconfig`, `rtguniqueimport.tblocation`, `rtguniqueimport.tbtour`, `rtguniqueimport.tbcountry`, `lhroscruleset`, etc.). Anything not on the allowlist is not replicated - this is the safety mechanism that prevents new schema elements from leaking out before DataHub is ready.
- **Message format: Avro** (preferred over JSON; binary, compact, already standardized in the GLS Kafka cluster).
- **Schema Registry:** let Debezium register the key and value schemas; the topic key is auto-derived from the table primary key.
- **Topics: compacted**, one per source table, so the latest value per key is retained. Include a version suffix in topic names per [[RouteFinder]] ("Kafka topic names include version suffix for schema evolution").
- **Initial snapshot:** rely on Debezium's first-start behaviour - it loads historical data, then streams ongoing WAL changes.
- **Version the connector config artifact** with the same `X.Y.Z` as `schema_update_rtg_X.Y.Z.sql` so the migration script and the connector ship together.

## Step 5: Coordinate Kafka infrastructure changes

[[route-finder-multi-shipit-version-support]] explicitly requires: when tables are added or removed, Kafka topics must be created/deleted accordingly. Schedule this with the Kafka platform team as part of the release.

## Step 6: Validate before release (Jenkins CI)

Per [[DatabaseSchemaEvolution]] and [[route-finder-multi-shipit-version-support]], the Jenkins compatibility pipeline must:

1. Provision a clean Docker Compose PostgreSQL, apply `schema_update_rtg_X.Y.Z.sql` in order, confirm no runtime errors.
2. Stand up a local Debezium Connect via Docker Compose and test the CDC connector configuration end-to-end.
3. Run Cucumber integration tests against the target database.
4. Run `route-finder-v1-smoketest` against all currently supported ShipIT versions.

## Step 7: Hand off to the ShipIt sink side

Ownership split is explicit ([[route-finder-datahub-sync]], [[KafkaCdc]]):

- **XB+ RTG team:** owns and creates the Debezium source connector and its versioned config.
- **ShipIt team:** owns the JDBC sink connector into [[DataHub]] and must update it for any new columns/tables. The sink connector must be forward compatible - ignore unknown columns/tables so it does not break if RTG ships a column it has not yet adopted.

Coordinate the cut-over: the source connector must not begin emitting changes for a new table/column until the sink connector and DataHub schema can accept them. The allowlist is the lever used to gate this.

## Step 8: Downstream flow (awareness)

[[ShipItSynchronizer]] reads from DataHub filtered by its supported version's tables/columns and pushes data to each customer's on-premise backend database.

## Gaps not yet captured in the wiki

- No concrete `application.properties` or Kafka Connect JSON template is stored anywhere in the wiki.
- No exhaustive table list for the v1 allowlist (the source page names representative tables but is not complete).
- No documented strategy for PostgreSQL replication slot lifecycle when an old connector is retired (e.g., when an old ShipIt version is dropped).

## Related Links

- [[route-finder-datahub-sync]] - original design document this playbook operationalizes
- [[route-finder-multi-shipit-version-support]] - version support policy and validation strategy
- [[KafkaCdc]] - CDC pipeline concept
- [[DebeziumConnector]] - source connector concept
- [[DatabaseSchemaEvolution]] - additive-only rules
- [[RouteFinder]] - the RTG service being synchronized
- [[Liquibase]] - DB migration tool (unified `xbp-rtg-rtg-liquibase` repo)
- [[DataHub]] - destination of synchronized data
- [[ShipItSynchronizer]] - downstream consumer
- [[ShipIt]] - host system for the sink side
