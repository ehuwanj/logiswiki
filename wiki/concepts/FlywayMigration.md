---
title: "Flyway Migration"
type: concept
tags: [shipit, flyway, database, migration, farm]
sources:
  - "[raw/shipit/ADR14 Manage db migrations for long and short releases - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200846921/ADR14+Manage+db+migrations+for+long+and+short+releases)"
last_updated: 2026-05-16
---

## Definition

Flyway is the database migration tool used for the ShipIT Farm backend database. It runs SQL migration scripts automatically during Kubernetes pod startup, applying changes in version-sorted order. ShipIT classic uses an Install4J-based updater for migrations; both tools consume the same SQL changeset files but require a compatible naming convention.

## Key Information

**Farm Usage:**
- Flyway runs during K8s pod start and applies `schema_update_fpcs_*.sql` files in version order
- Files must follow a numeric-extractable naming convention for Flyway to determine order

**Naming Convention (ADR14, Option 5):**
- Long release: `schema_update_fpcs_X.Y.Z.sql` (e.g., `schema_update_fpcs_4.0.1.sql`)
- Farm RC: `schema_update_fpcs_X.Y.Z.W__X.Y.fZ-rcW.sql` (e.g., `schema_update_fpcs_4.0.1.1__4.0.f1-rc1.sql`)
- Flyway extracts `X.Y.Z.W` as the numeric version for ordering
- Short-release scripts must be idempotent (may be re-applied across minor release migrations)

**Difference from Classic ShipIT:**
- Classic: Install4J updater processes SQL files during installation/update
- Farm: Flyway processes during pod startup
- Both use same SQL files; naming convention must satisfy both tools' requirements

## Related Links

- [[ShipItFarm]] - uses Flyway for DB migrations
- [[ShipIt]] - uses Install4J updater for classic DB migrations
- [[FarmReleaseCycle]] - Farm release versioning that Flyway must handle
- [[Liquibase]] - Route Finder's DB migration tool (for comparison)
- [[shipit-adr14-db-migration-versioning]] - ADR choosing naming convention
