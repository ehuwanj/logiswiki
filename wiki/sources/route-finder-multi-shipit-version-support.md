---
title: "Support Multiple ShipIt versions in Route Finder"
type: source
tags: [routing, versioning, compatibility, route-finder]
sources:
  - "[raw/routing/Support Multiple ShipIt versions in Route Finder - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166061448/Support+Multiple+ShipIt+versions+in+Route+Finder)"
last_updated: 2026-05-16
---

## Summary

Route Finder must simultaneously support the three most recent ShipIT major versions. This document defines the version support policy, database evolution rules, REST API versioning, and a compatibility verification strategy to achieve this. The shared RTG database must remain backward-compatible across all supported versions using an additive-only schema evolution approach. Patch releases cover bug fixes only; minor releases introduce new compatible features.

## Key Claims

- Route Finder supports the three most recent ShipIT versions simultaneously; patch versions are automatically supported (no breaking changes).
- Database schema evolution is strictly additive: no table or column may be removed while any dependent Route Finder version is still supported.
- Deprecated tables/columns must be documented and retained until the last dependent version is dropped.
- REST API versioning: currently v1 only; breaking changes require a new API version (e.g., v2) while older versions remain available.
- Compatibility verification runs in Jenkins CI pipeline per ShipIT release: schema migration script, local Kafka/Debezium CDC test, Cucumber integration tests.
- Kafka infrastructure must be updated when adding/removing tables (new topics created/deleted accordingly).

## Evidence and Notes

- Version-specific migration scripts: `schema_update_rtg_3.1.0.sql`, `schema_update_rtg_4.0.0.sql` etc.
- Data pipeline: RTG DB -> CDC Connector -> Kafka Topics -> Sink Connector -> DataHub DB.
- Backward-compatible API changes allowed: adding optional request parameters, adding optional response fields, introducing new endpoints.
- Smoke test repo `apigee-edge-route-finder-v1` should be version-tagged to match Route Finder releases.
- Jenkins job `route-finder-v1-smoketest` to be enhanced to test all supported ShipIT versions.

## Related Links

- [[RouteFinder]] - the Route Finder service
- [[SemanticVersioning]] - versioning strategy (MAJOR.MINOR.PATCH)
- [[DatabaseSchemaEvolution]] - additive-only schema evolution rules
- [[DataHub]] - ShipIT DataHub receiving sync data
- [[KafkaCdc]] - CDC pipeline used for DB synchronization
- [[route-finder-shipit-semver-guide]] - semantic versioning details
- [[route-finder-datahub-sync]] - data synchronization approach
