---
title: "ShipIT ADR3: Deploy Synchronizer for Webbackend Farm"
type: source
tags: [shipit, farm, synchronizer, kafka, adr]
sources:
  - "[raw/shipit/ADR3 Deploy Synchronizer for Webbackend Farm - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200844071/ADR3+Deploy+Synchronizer+for+Webbackend+Farm)"
last_updated: 2026-05-16
---

## Summary

This ADR addresses how to deploy the ShipIT Synchronizer when multiple backend instances run in parallel sharing a single database. The Synchronizer currently runs as one instance per backend, reading Kafka data via Data Provider / Param Provider and writing to the database. Three options were considered: deploy alongside each backend, deploy as an independent application, or integrate Synchronizer functionality directly into the backend as a Kafka Consumer. No explicit outcome is documented in the source.

## Key Claims

- Requirement: all active backend instances must receive updates/commands; database changes must happen only once (not duplicated).
- Problem: with N backend pods, option 1 (alongside backend) creates N Synchronizer instances reading the same data.
- Option 3 (Kafka Consumer embedded in backend): requires solving idempotency or write-once guarantees; high development effort.
- Current Synchronizer design uses Kafka REST proxy polling (not native consumer) due to historical port-opening constraints in customer environments; Farm runs in AWS so these constraints no longer apply.
- ADR3 outcome is required before implementing ADR2 (AS user/shipper sync via datahub-assignment-injector).

## Evidence and Notes

- Synchronizer reads: master data via data-provider, parameter changes via param-provider.
- Commands sent to backend: change application parameters, set backend active/inactive -> result in database changes, not direct invocations.
- Option 2 (independent Synchronizer): operational complexity depends on ADR2 hosting technology (EKS makes this easy).
- Out of scope: ensuring write idempotency or single-writer guarantee (deferred to follow-up ADR).

## Related Links

- [[ShipItSynchronizer]] - the Synchronizer component
- [[ShipItFarm]] - Farm context
- [[KafkaCdc]] - Kafka integration
- [[DataHub]] - data source for Synchronizer
- [[shipit-adr2-backend-server-technology]] - EKS hosting decision
- [[shipit-adr4-oauth2-integration]] - OAuth2 integration (follow-up)
- [[shipit-as-user-sync-adr]] - revised user sync (ShipIT-ADR2)
