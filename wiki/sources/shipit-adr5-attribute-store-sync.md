---
title: "ShipIT ADR5: Sync Attribute Store Users to Farm"
type: source
tags: [shipit, farm, kafka, attribute-store, adr]
sources:
  - "[raw/shipit/ADR5 Sync Attribute Store users to Farm - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200842845/ADR5+Sync+Attribute+Store+users+to+Farm)"
last_updated: 2026-05-16
---

## Summary

Following ADR4's decision to push user changes via Kafka, this ADR defines the Kafka topic structure. Two options were evaluated: one aggregated topic with all user info, or separate normalized topics matching the AS database structure. Separate topics (Option 2) were chosen because they minimize transformation complexity and GPP development effort while providing precise change granularity.

## Key Claims

- Decision: Option 2 - provide separate normalized Kafka topics.
- Topics: `shipit_attributestore_user` (user master data, stripped of internal IDs) and `shipit_attributestore_user-shipper` (user-shipper assignments using OID and contact_id).
- Attribute Store uses Debezium CDC to replicate internal tables to base topics; public topics are enriched/filtered versions.
- Public topics hide internal AS IDs (internal user ID, internal shipper ID); use OID for users and contact_id for shippers.
- Option 1 (aggregated): complex join of multiple base topics required; hard to detect shipper changes in lists of 1000+ entries.
- Additional topics possible in future (e.g., user roles).

## Evidence and Notes

- Base topic for users: `shipit_debezium-attributestore_user`.
- Base topic for user-shipper: `shipit_debezium-attributestore_user-shipper` + `shipit_debezium-attributestore_shipper`.
- Message format includes `op` (c/u/d), `ts_ms`, `before`, `after` fields (Debezium envelope).
- Example user message: `{"op": "u", "before": {"oid": "...", "volumetrictype": "NONCALIBRATED"}, "after": {...}}`.
- Example user-shipper message: `{"before": {"user_oid": "...", "shipper_contact_id": "..."}, "after": {...}}`.

## Related Links

- [[AttributeStore]] - GPP's user management service providing the topics
- [[ShipItFarm]] - Farm consuming the topics
- [[KafkaCdc]] - CDC infrastructure
- [[DebeziumConnector]] - Debezium used for AS CDC
- [[shipit-adr4-oauth2-integration]] - parent decision (Option 3c)
- [[shipit-adr13-farm-user-sync]] - farm-specific user filtering
- [[shipit-adr16-usershipper-sync-rework]] - later rework of this sync
