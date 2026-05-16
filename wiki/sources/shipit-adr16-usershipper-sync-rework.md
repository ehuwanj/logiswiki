---
title: "ShipIT ADR16: Rework User/Shipper Sync"
type: source
tags: [source, shipit, farm, user-sync, datahub, kafka-streams, adr]
sources:
  - "[raw/shipit/ADR16 Rework user/shipper sync - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200945248/ADR16+Rework+user+shipper+sync)"
last_updated: 2026-05-16
---

## Summary

The original AS -> Synchronizer sync (ADRs 4-7) had many bugs and incomplete implementations. ADR16 proposes a comprehensive architectural rework: introduce a new `datahub-assignment-injector` component that uses Kafka Streams to consume both Attribute Store and DataHub Debezium topics, store user management tables in DataHub, and propagate changes to backends via the Synchronizer. This replaces both the `as-data-provider` and `shipper-assignment-injector` microservices.

## Key Claims

- New component: `datahub-assignment-injector` replaces `as-data-provider` and `shipper-assignment-injector`.
- New DataHub tables: role, user, user_backend, user_backend_role, user_shipper, user_parameter.
- Architecture: AS (Debezium) -> Kafka -> DataHub-Assignment-Injector -> DataHub DB -> Synchronizer -> Backend DB.
- Kafka Streams used for joining AS and DataHub Debezium topics.
- Unprocessable records control table to be removed.
- Synchronizer feeds from DataHub topics (not directly from AS) and propagates changes to backend.
- "All sync is relevant to the farm_id the user/shipper is assigned to."

## Evidence and Notes

- Key abbreviations: AS=Attribute Store, BK=Business Key, CC=Control Center, DH=Datahub, HK=Housekeeping.
- Comprehensive use-case table covers: create/delete/deactivate user, change role/farm/shipper, set parameters, add/remove hazardous goods.
- 66 related Jira issues tracked under epic GS-7223.
- Key open bugs: DH Assignment Injector NullPointerException (GS-7669), assign inactive shipper (GS-7617), haz goods sync ordering (GS-7603).
- Housekeeping (GS-7065): cleanup orphaned users without role assignments.

## Related Links

- [[ShipItFarm]] - Farm context
- [[DataHub]] - now holds user management tables
- [[ShipItSynchronizer]] - consumes DataHub topics, pushes to backend
- [[AttributeStore]] - source of user/shipper data
- [[KafkaCdc]] - Kafka Streams / Debezium infrastructure
- [[shipit-adr5-attribute-store-sync]] - original topic structure
- [[shipit-adr7-shipper-assignment-automation]] - shipper assignment automation
- [[shipit-as-user-sync-adr]] - ShipIT-ADR2 formally documenting this rework decision
- [[DatahubAssignmentInjector]] - concept page for the new component
