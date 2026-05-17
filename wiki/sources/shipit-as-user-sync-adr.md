---
title: "ShipIT ADR: AS User/Shipper Assignment Synchronization into ShipIT"
type: source
tags: [shipit, farm, user-sync, datahub-assignment-injector, adr]
sources:
  - "[raw/shipit/ShipIt-ADR2 AS UserShipper assignment synchronization into ShipIT - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200837788/ADR2+AS+User+Shipper+assignment+synchronization+into+ShipIT)"
last_updated: 2026-05-16
---

## Summary

This ADR formally documents the decision to rework the user/shipper synchronization (initially designed in ADR5 via direct AS Kafka topics -> Synchronizer) into a robust solution using a new `datahub-assignment-injector` microservice (detailed in ADR16). The original solution proved flawed with many synchronization failures due to timing/ordering issues and incomplete coverage of use cases.

## Key Claims

- Decision: replace the existing AS -> Synchronizer direct sync with a new process via datahub-assignment-injector.
- New route: AS (Debezium events) -> Kafka -> datahub-assignment-injector -> DataHub -> Synchronizer -> backend.
- Replaces `as-data-provider` and `shipper-assignment-injector` microservices with a single component.
- Advantage: datahub now holds user management tables, enabling multi-backend user sharing.
- Root cause of original problems: shippers sync to ShipIT (via datahub) could take over 24h, causing timing errors in AS sync logic.
- Constraint: requires ADR3 (Synchronizer as native Kafka consumer) to be implemented first.
- Local and web backends still use ShipIT frontend for user management (not via datahub); this could be extended later.

## Evidence and Notes

- Original solution label in Jira: `UserShipperFarmSync`.
- Proof: GS-7129 and related bugs showed many functional failures.
- Key insight: "Datahub is the ShipIT component responsible for managing different backend instances; all incoming information from outside systems should be synchronized through datahub."
- Epic: GS-7223 (66 related issues).

## Related Links

- [[ShipItFarm]] - Farm context
- [[DataHub]] - central synchronization point
- [[ShipItSynchronizer]] - downstream consumer
- [[AttributeStore]] - source of truth for user data
- [[shipit-adr5-attribute-store-sync]] - original (flawed) design
- [[shipit-adr16-usershipper-sync-rework]] - technical design of the new approach
- [[shipit-adr3-synchronizer-deployment]] - prerequisite ADR
- [[DatahubAssignmentInjector]] - concept page for the component designed in this ADR
