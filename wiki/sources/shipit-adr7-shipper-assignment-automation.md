---
title: "ShipIT ADR7: Remove Shipper Assignments from Control Center"
type: source
tags: [shipit, farm, shipper, datahub, adr]
sources:
  - "[raw/shipit/ADR7 Remove shipper assignments from Control Center - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200843123/ADR7+Remove+shipper+assignments+from+Control+Center)"
last_updated: 2026-05-16
---

## Summary

In current ShipIT, shippers must be manually assigned to a backend via Control Center before user data can be synced. For the Farm, this manual step should be eliminated: shipper assignments should be derived automatically from the shippers assigned to Farm users in the Attribute Store. The core challenge is that the Synchronizer uses the DataHub internal shipper ID while Attribute Store only knows the contact ID. Decision: Option 3 - fill the DataHub `shipper_backend` table automatically from Attribute Store user-shipper topic, keeping the Synchronizer process unchanged.

## Key Claims

- Decision: Option 3 - fill DataHub `shipper_backend` table from Attribute Store Kafka topic; Synchronizer process stays unchanged.
- The `shipper_backend` table in DataHub tracks which shippers are assigned to which backend; currently managed manually via Control Center.
- Attribute Store's `shipit_attributestore_user-shipper` topic (from ADR5) is reused as the trigger for shipper assignment.
- A new DataHub consumer is required to read the user-shipper topic and update `shipper_backend` (has read/write access to DataHub DB).
- Synchronizer two-step process remains: (1) param-provider gives assigned shippers list, (2) data-provider gives shipper master data.

## Evidence and Notes

- Option 1 (add datahub shipper ID to AS): rejected because business users cannot provide internal datahub IDs.
- Option 2 (mapping service via data/param-provider): rejected because these services only read from Kafka, not DB.
- Option 4 (fetch via contact_id from data-provider): complex; requires scanning full Kafka topic partitioned by datahub shipper ID.
- Concern: param-provider will return a very long shipper list for Farm; pagination may be needed.
- Decision meeting date: Aug 22, 2023.

## Related Links

- [[ShipItFarm]] - Farm context
- [[AttributeStore]] - provides user-shipper assignments
- [[DataHub]] - contains shipper_backend table
- [[ShipItSynchronizer]] - uses shipper assignments
- [[shipit-adr5-attribute-store-sync]] - Kafka topic used for assignment trigger
- [[shipit-adr16-usershipper-sync-rework]] - later comprehensive rework
