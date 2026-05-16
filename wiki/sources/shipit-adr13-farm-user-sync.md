---
title: "ShipIT ADR13: Only Sync Farm Users from Attribute Store"
type: source
tags: [source, shipit, farm, user-sync, kafka, adr]
sources:
  - "[raw/shipit/ADR13 Only sync Farm users from attribute-store - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200846022/ADR13+Only+sync+Farm+users+from+attribute-store)"
last_updated: 2026-05-16
---

## Summary

Attribute Store holds users for multiple systems (ShipIT Farm, GPP, others); the Farm should only sync users that belong to it. The previous approach of consuming all users without filtering proved problematic. Decision: Option 2 - separate initial load (via AS REST API when user_role is first assigned to this Farm) combined with ongoing updates via Kafka topics, filtered by farm_id.

## Key Claims

- Decision: Option 2 - initial load via AS REST API (triggered by new user_role with matching farm_id) + Kafka updates.
- Problem with earlier approach: topics lacked farm_id for filtering; a user assigned later could not trigger re-read of already-skipped user record.
- user_role topic is declared as the "leading topic": when a new user_role with matching farm_id appears, check if user is known; if not, fetch full user details from AS REST API.
- For other topics (user_shipper etc.): skip message if user is not yet known to this Farm.
- Race-condition risk acknowledged: role assignment triggering initial load while shipper assignment also arrives in parallel.
- AS REST API endpoints already expose required information; no new endpoints needed; communication stays within same Kubernetes cluster.
- Option 1 (AS only pushes Farm users): rejected - too much logic in AS; topics become Farm-specific and not reusable.
- Option 3 (Kafka Streams join): rejected - complex join, Kafka as DB anti-pattern.
- Option 4 (sync all users, filter by role): rejected - too many orphan users in Farm DB; security risk.

## Evidence and Notes

- FARM-228: tracks the implementation.
- user_role topic message includes `farm_id` field (e.g., "main").
- Manually added user_roles in Farm DB should not be removed by the sync process.
- Attribute Store topics: `user`, `user-role`, `user_shipper` (from ADR5).

## Related Links

- [[ShipItFarm]] - Farm context
- [[AttributeStore]] - source of user data
- [[ShipItSynchronizer]] - consumer of the synced data
- [[shipit-adr5-attribute-store-sync]] - original Kafka topic structure
- [[shipit-adr12-sandbox-user-config]] - farm_id (main/sandbox) definition
- [[shipit-adr16-usershipper-sync-rework]] - later rework
