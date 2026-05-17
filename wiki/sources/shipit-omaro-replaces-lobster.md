---
title: "ShipIT ADR: Omaro to Replace Lobster"
type: source
tags: [shipit, omaro, lobster, kafka, parcel-data, adr]
sources:
  - "[raw/shipit/ShipIt-ADR1 Omaro to replace Lobster - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200837784/ADR1+Omaro+to+replace+Lobster)"
last_updated: 2026-05-16
---

## Summary

ShipIT currently uses Lobster_data to consume parcel data from a Kafka topic and transmit TRN files to Unique. Lobster is a closed platform with expensive licensing, poor release management, and difficult custom logic. A new microservice called "Omaro" (Esperanto for lobster) will replace it, leveraging existing Kubernetes infrastructure, Kafka, and GLS data models with proper versioning and monitoring.

## Key Claims

- Decision: create new "Omaro" microservice to replace Lobster_data for ShipIT parcel data transmission to Unique.
- Rationale: Lobster is costly (license), hard to customize, has no version control between test/production, and changes are error-prone.
- Omaro reads from Kafka parcel data topic, maps data to TRN file format, uploads to FTP server for Unique pickup.
- Current flow: Data Receiver (REST per shipment) -> Kafka topic -> (a) Datahub Injector (to DataHub DB) + (b) Lobster (TRN to Unique).
- Potential to eliminate data-receiver and datahub-injector by consolidating into Omaro.
- Name: "Omaro" = Esperanto for lobster; proof-of-concept planned in production Kafka (no production impact, just consumes from topic).

## Evidence and Notes

- Multiple parallel Lobster profiles exist for TRN and TRX formats across countries; maintenance is high complexity.
- Lobster has two completely separate instances (test, prod) with no automated sync; manual replication required.
- Kubernetes infrastructure advantages: grafana monitoring, better logging, auto-scaling, load balancing, retry handling.
- Proof-of-concept: consume from production Kafka topic, write TRN files to a separate non-production server.

## Related Links

- [[ShipIt]] - ShipIT system context
- [[KafkaCdc]] - Kafka infrastructure used by Omaro
- [[OmaroMicroservice]] - concept page for Omaro
