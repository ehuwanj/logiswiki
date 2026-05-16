---
title: "Omaro Microservice"
type: concept
tags: [shipit, omaro, kafka, parcel-data, unique, microservice]
sources:
  - "[raw/shipit/ShipIt-ADR1 Omaro to replace Lobster - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200837784/ADR1+Omaro+to+replace+Lobster)"
last_updated: 2026-05-16
---

## Definition

Omaro is a ShipIT microservice that replaces the Lobster_data platform for transmitting parcel data to Unique. It consumes parcel data from a Kafka topic, maps it to TRN file format, and uploads the result to an FTP server where Unique picks it up. The name "Omaro" means "lobster" in Esperanto.

## Key Information

**Purpose:**
- Reads parcel data from Kafka topic (same topic previously consumed by Lobster)
- Maps ShipIT data model to TRN (and potentially TRX) file format
- Uploads TRN files to FTP server for Unique
- Runs on existing EKS infrastructure with Grafana monitoring

**Why Lobster was Replaced:**
- Lobster_data is a closed platform with expensive licensing
- No proper release management: changes applied directly in test instance, manually replicated to production
- No version control; no git history; error-prone manual sync between test/production
- Multiple separate profiles (TRN/TRX per country) duplicating mapping logic

**Architecture Opportunities:**
- Potential to eliminate `data-receiver` microservice (currently splits one REST message into multiple Kafka messages per unit)
- Potential to integrate DataHub persistence directly, eliminating `datahub-injector` microservice
- One single Kafka topic consumer instead of multiple parallel Lobster profiles

**Deployment:**
- Proof-of-concept: consume from production Kafka topic, write TRN to separate non-production FTP server (zero production impact)

## Related Links

- [[ShipIt]] - ShipIT system context
- [[ShipItFarm]] - EKS hosting context for Omaro
- [[AmazonEKS]] - EKS infrastructure where Omaro runs
- [[KafkaCdc]] - Kafka infrastructure
- [[DataHub]] - DataHub injector potentially consolidatable into Omaro
- [[shipit-omaro-replaces-lobster]] - ADR source document
