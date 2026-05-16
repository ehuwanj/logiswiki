---
title: "Liquibase"
type: concept
tags: [liquibase, database, migrations, route-finder, crossborder-plus]
sources: ["[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)"]
last_updated: 2026-05-10
---

## Definition

Liquibase is an open-source database migration framework used by Route Finder (RTG) to manage and apply database schema changes. It uses changelog files to track and apply incremental SQL changes to the database. In the ShipIt integration context, Liquibase migration files must also be translated into ShipIt-compatible versioned SQL files for on-premise deployments.

## Key Information

- **Usage in Route Finder**: Two Liquibase projects manage RTG database migrations:
  - `xbp-rtg-rtg-liquibase` - manages the `routing` schema
  - `xbp-rtg-rtg-unique-import` - manages the `rtguniqueimport` schema
- **ShipIt migration format**: ShipIt uses a single versioned SQL file per release (e.g., `schema_update_fpcs_X.Y.Z[.W]__description.sql`), managed by the Component Updater. A new versioned SQL file for Route Finder (`schema_update_rtg_X.Y.Z.sql`) must be created to bridge the two systems.
- **Schemas covered**: The new migration file should cover `cron`, `routing`, and `rtguniqueimport` schemas (the three required for Route Finder execution).
- **Backward compatibility requirement**: Since three ShipIt versions of Route Finder share one RTG database, all schema changes must be additive. Breaking changes (column removal, renaming, type changes, primary key modifications) are not permitted while older versions are active.
- **Relationship to CDC**: Liquibase migrations define the RTG database structure; the Debezium CDC connector's allowlist configuration is versioned to match each Liquibase migration release.

## Related Links

- [[RouteFinder]] - application whose DB migrations are managed by Liquibase
- [[DebeziumConnector]] - reads the database structure created by Liquibase migrations
- [[KafkaCdc]] - synchronization pipeline built on the database structure
- [[DataHub]] - receives migrations via versioned SQL files
- [[route-finder-datahub-sync]] - source document describing the migration approach
