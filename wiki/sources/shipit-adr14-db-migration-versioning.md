---
title: "ShipIT ADR14: Manage DB Migrations for Long and Short Releases"
type: source
tags: [source, shipit, farm, flyway, db-migration, versioning, adr]
sources:
  - "[raw/shipit/ADR14 Manage db migrations for long and short releases - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200846921/ADR14+Manage+db+migrations+for+long+and+short+releases)"
last_updated: 2026-05-16
---

## Summary

ShipIT has two migration mechanisms: Install4J-based updater for classic ShipIT (SQL changeset files per version) and Flyway for Farm (runs during Kubernetes pod startup). The Farm release version format (`X.Y-fN-rcM`) is not directly supported by Flyway's numeric-order versioning. Decision: Option 5 - uniform notation where the SQL file name encodes both the ShipIT version and Farm release info using numeric fields, requiring minor changes to the updater but no Java code changes to Flyway.

## Key Claims

- Decision: Option 5 - uniform versioning notation in SQL filenames compatible with both Flyway and the Install4J updater.
- SQL filename format: `schema_update_fpcs_X.Y.Z[.W]__description.sql` where Z and W encode Farm release info.
- Long release (classic): `schema_update_fpcs_4.0.1.sql` (W absent).
- Short release (Farm RC): `schema_update_fpcs_4.0.1.1__4.0.f1-rc1.sql` (Z=farm#, W=RC#, description=Farm version).
- Flyway extracts numeric version from filename without custom Java code changes (only property configuration needed).
- Updater is adjusted to run short-release files first within a migration.
- Short-release SQL scripts must be idempotent since they may be re-applied across minor releases.
- Option 1 (number-only versioning): rejected - would abandon the Farm version notation.
- Option 2 (Java Flyway extension): rejected - complex and affects both Flyway and updater.
- Option 3 (completely independent DB versioning): rejected - loses link between SQL filename and ShipIT version.
- Option 4 (control file): rejected - too much manual overhead and implementation effort for both tools.

## Evidence and Notes

- Flyway picks up files in `schema_update_fpcs_*` folder during K8s pod start.
- Version conflict note: `4.0.1` (-> 4.0.1.0) and `4.0.f1-rc0` (-> 4.0.1.0) would conflict; RC version 0 should never exist.

## Related Links

- [[ShipItFarm]] - Farm context
- [[FlywayMigration]] - Flyway DB migration concept
- [[Liquibase]] - Route Finder's migration tool (for comparison)
- [[shipit-adr10-farm-release-cycles]] - Farm release versioning that created this problem
