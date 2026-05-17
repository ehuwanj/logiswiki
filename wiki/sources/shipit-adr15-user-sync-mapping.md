---
title: "ShipIT ADR15: User Sync - Which Information to Take from Which Place"
type: source
tags: [shipit, farm, user-sync, kafka, synchronizer, adr]
sources:
  - "[raw/shipit/ADR15 User sync - which information to take from which place - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200932333/ADR15+User+sync+-+which+information+to+take+from+which+place)"
last_updated: 2026-05-16
---

## Summary

This document is a detailed mapping table specifying which Kafka topics drive which actions in the Synchronizer for each business event in the Attribute Store. It covers the user topic, user_role topic, user_shipper topic, parameter setting topic, AS REST API, and the Shipper Assignment Injector. This is a reference document for implementing the user sync Synchronizer logic.

## Key Claims

- user_role topic is the master trigger: creates/removes user records; drives all downstream actions.
- user topic: only used for active/inactive state changes after user creation.
- user_shipper topic: adds/removes role assignments for shipper-user combinations.
- parameter topic: adds/changes/removes user_parameter entries.
- AS REST API: called for initial load of new users (when farm_id is unknown to Farm).
- Shipper Assignment Injector: assigns shippers to backend (DataHub level).
- Kafka message format documented: user, user_role, user_shipper, and parameter topics with Avro schema namespaces.

## Evidence and Notes

- user topic Avro schema: `eu.glsgroup.attributestore.dataprovider.avro.user.User` with fields: active, oid, volumetrictype.
- user_role topic Avro: `eu.glsgroup.attributestore.dataprovider.avro.userroleassignment.RoleAssignment` with: user_oid, role_name, role_system, farm_id.
- user_shipper topic Avro: `eu.glsgroup.attributestore.dataprovider.avro.usershipperassignment.ShipperAssignment` with: shipper_contact_id, user_oid.
- parameter topic: `eu.glsgroup.attributestore.dataprovider.avro.parameter.ParameterSettings` with: key, type, param_key, param_value.

## Related Links

- [[ShipItFarm]] - Farm context
- [[AttributeStore]] - source of all user data
- [[ShipItSynchronizer]] - consumer of these topics
- [[shipit-adr13-farm-user-sync]] - filtering strategy
- [[shipit-adr16-usershipper-sync-rework]] - rework of this sync
