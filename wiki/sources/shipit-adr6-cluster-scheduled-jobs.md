---
title: "ShipIT ADR6: Make Scheduled Jobs Cluster Ready"
type: source
tags: [shipit, farm, quartz, scheduling, adr]
sources:
  - "[raw/shipit/ADR6 Make scheduled jobs cluster ready - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200842639/ADR6+Make+scheduled+jobs+cluster+ready)"
last_updated: 2026-05-16
---

## Summary

The ShipIT backend uses Quartz scheduler with 20+ jobs, some of which must not run in parallel (database-writing jobs) while others are safe to run on every instance. For the Farm cluster, this ADR decides how to prevent duplicate job execution. Decision: Option 1 - deploy a single separate backend instance (single-node) with only performance-intensive non-parallel jobs enabled; all jobs remain in the same codebase and can be individually disabled via configuration.

## Key Claims

- Decision: Option 1 - separate single-node backend deployment with only database-writing jobs enabled; never reachable from outside the cluster.
- Jobs that must NOT run in parallel: Retry Shipment Transmission, Retry Shipment Cancellation, Aged Shipments, Admin Password Reset, Number Range Validation, Fetch Backend Name, Retry Update Shipment Transmission, Execute Create Batch Shipments, Transmit single Shipment (and unit), Transmit cancellation of single Shipment Unit.
- Jobs safe to run in parallel: Ping, Session Store Cleanup, Heartbeat, Parcel Log Cleaner.
- Jobs disabled for Farm (frontend-only): Old Backup Cleaner, Backup Database, Update Checker, Update Install, Backend Details, Clear ImEx Folders, Cleanup ImEx Executions, Import Export, Stale Autologin Token Cleanup, Global Date Reset, Certificate Renewal, Guarantee Cleanup.
- Option 2 (Quartz clustering via shared DB): rejected because Quartz currently stores metadata in RAM and the heaviest jobs could impact all backend pods if co-located.
- Future option: refactor jobs into a dedicated application, but avoided now due to impact on local installations.

## Evidence and Notes

- Quartz library at `https://www.quartz-scheduler.org/` used for scheduling in ShipIT backend.
- Jobs marked with `*` are not scheduled but triggered explicitly (e.g., Send Pending Mails, Backup database now).
- Aged Shipments is specifically called out as a long-running, high-load job motivating this decision.
- Configuration-only disabling approach preserves local installation compatibility (local install = 1-node "cluster").

## Related Links

- [[QuartzScheduler]] - Quartz job scheduler concept
- [[ShipItFarm]] - Farm cluster context
- [[ShipIt]] - ShipIT backend
- [[shipit-adr2-backend-server-technology]] - EKS deployment of backend
