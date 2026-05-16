---
title: "Quartz Scheduler"
type: concept
tags: [quartz, scheduling, shipit, cluster, jobs]
sources:
  - "[raw/shipit/ADR6 Make scheduled jobs cluster ready - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200842639/ADR6+Make+scheduled+jobs+cluster+ready)"
last_updated: 2026-05-16
---

## Definition

Quartz is the Java job scheduling library used in the ShipIT backend to run 20+ periodic tasks. For the ShipIT Farm (cluster mode), jobs that write to the database must not run on more than one instance simultaneously. The solution chosen (ADR6) is to deploy a single separate backend pod with only the performance-intensive, non-parallelizable jobs enabled.

## Key Information

**Farm Cluster Strategy (ADR6):**
- Separate single-node backend deployment (never externally reachable) with only database-writing jobs enabled
- All jobs remain in the same codebase; individually disabled via configuration
- Regular Farm backend pods run without database-writing scheduled jobs

**Jobs That Must NOT Run in Parallel (enabled on single-node pod):**
- Retry Shipment Transmission, Retry Shipment Cancellation
- Aged Shipments, Admin Password Reset, Number Range Validation
- Fetch Backend Name, Retry Update Shipment Transmission
- Execute Create Batch Shipments, Transmit single Shipment/Unit, Transmit cancellation

**Jobs Safe to Run in Parallel (enabled on all pods):**
- Ping (DataHub availability check), Session Store Cleanup, Heartbeat (JVM stats), Parcel Log Cleaner

**Jobs Disabled for Farm (frontend-only):**
- Old Backup Cleaner, Backup Database, Update Checker, Update Install, Backend Details
- Clear ImEx Folders, Cleanup ImEx Executions, Import Export, Stale Autologin Token Cleanup
- Global Date Reset, Certificate Renewal, Guarantee Cleanup

**Rejected Option (Quartz Clustering):**
- Quartz clustering mode uses shared DB for job coordination
- Rejected because: Quartz currently uses RAM for metadata (needs DB change); performance-intensive jobs would impact regular pods

## Related Links

- [[ShipItFarm]] - Farm cluster where Quartz scheduling is adapted
- [[ShipIt]] - original ShipIT backend using Quartz
- [[shipit-adr6-cluster-scheduled-jobs]] - ADR6 source document
