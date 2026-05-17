---
title: "ShipIT ADR12: Configuration of Users in Sandbox"
type: source
tags: [shipit, farm, sandbox, user-management, adr]
sources:
  - "[raw/shipit/ADR12 Configuration of users in Sandbox - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200844761/ADR12+Configuration+of+users+in+Sandbox)"
last_updated: 2026-05-16
---

## Summary

The Farm has two logical instances: main (production) and sandbox (test). This ADR defines how to distinguish users between them. The `farm` table's ID column changes from integer to varchar with two entries: "main" and "sandbox". User role assignments reference the farm_id so users can be assigned to main, sandbox, or both.

## Key Claims

- Farm IDs: "main" (productive) and "sandbox" (sandbox farm in test mode); stored as varchar in the `farm` table.
- A user_role assignment can exist twice: once for sandbox, once for main.
- Users synchronized from Attribute Store must be filtered by farm_id to go to the correct backend.
- Attribute Store frontend adds a mandatory "farm" field (list with "main"/"sandbox") at user creation; optional on update.
- Deleting a user in AS deletes both user_role entries (main and sandbox).
- `farm_id` is removed from `user_shipper` table (not needed for shipper assignments).

## Evidence and Notes

- GS-6321: Farm ID reflects main farm or sandbox.
- GS-5737: Replicate user roles towards Farm (topic: shipit_attributestore_user-role).
- FARM-228: Only sync users with the correct farm_id.
- GS-6322: AS user endpoints can handle the farm_id field.
- PIA-3464: AS Frontend handles sandbox/production user assignment with role split into 2 fields.

## Related Links

- [[ShipItFarm]] - Farm context with main/sandbox distinction
- [[AttributeStore]] - manages farm_id per user
- [[shipit-adr13-farm-user-sync]] - Farm user filtering via farm_id
