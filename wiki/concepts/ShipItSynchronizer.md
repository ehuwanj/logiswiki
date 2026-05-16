---
title: "ShipIt Synchronizer"
type: concept
tags: [shipit, synchronizer, data-sync, datahub, spring-boot, farm, kafka, eks]
sources:
  - "[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)"
  - "[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"
  - "[raw/shipit/ADR3 Deploy Synchronizer for Webbackend Farm - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200838534/ADR3+Deploy+Synchronizer+for+Webbackend+Farm)"
  - "[raw/shipit/ADR16 Rework usershipper sync - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200851031/ADR16+Rework+usershipper+sync)"
last_updated: 2026-05-16
---

## Definition

The ShipIt Synchronizer is a Spring Boot application that applies data updates to ShipIt backend databases. For on-premise deployments, it runs alongside each backend instance and consumes from DataHub. For ShipIT Farm (cloud), it runs as an independent application in Amazon EKS, consuming from DataHub Kafka topics (populated by the datahub-assignment-injector). It is the final step in both the routing data pipeline and the user/shipper assignment pipeline.

## Key Information

**On-Premise (Classic):**
- Spring Boot application, one per ShipIt backend instance, deployed at each customer site
- Reads routing data from the DataHub database and synchronizes to local ShipIt backend DB
- Consumes messages via Data Provider and Parameter Provider components
- Extends to consume routing data from DataHub DB during Route Finder integration

**ShipIT Farm (Cloud, ADR3):**
- Deployed as an **independent application in Amazon EKS** (not embedded in the backend pod)
- Chosen over embedding in backend pod (Option 2) and Kafka-only approach (Option 3)
- Connects to Farm Aurora Serverless v2 database directly
- Eliminates per-customer deployment complexity

**User/Shipper Sync (ADR16 - datahub-assignment-injector approach):**
- Prior approach: `as-data-provider` + `shipper-assignment-injector` consumed separately from Attribute Store Kafka topics
- New approach: `datahub-assignment-injector` (Kafka Streams) joins AS and DataHub Debezium topics, writes to DataHub user management tables
- Synchronizer then reads from DataHub user management tables instead of consuming AS topics directly
- Eliminates `as-data-provider` and `shipper-assignment-injector` microservices

**Shared behavior:**
- Version-aware: each ShipIt version's Synchronizer reads only tables/columns compatible with its Route Finder version
- OAuth2 integration: exchanges credentials (backend name + installation token) for access token at startup
- ShipIt team owns the Synchronizer and its extensions

## Related Links

- [[DataHub]] - data source for the Synchronizer (routing data and user management tables)
- [[KafkaCdc]] - upstream synchronization pipeline feeding DataHub
- [[ShipIt]] - parent system that owns and deploys the Synchronizer
- [[ShipItFarm]] - Farm cloud deployment context (EKS-hosted Synchronizer)
- [[AttributeStore]] - source of user/shipper data via datahub-assignment-injector
- [[ShipItOAuth2]] - authentication mechanism used by the Synchronizer
- [[RouteFinder]] - source of routing data being synchronized
- [[AmazonEKS]] - Farm hosting platform for Synchronizer
- [[route-finder-datahub-sync]] - source document describing Synchronizer's role
- [[shipit-adr3-synchronizer-deployment]] - Farm Synchronizer deployment decision
- [[shipit-adr16-usershipper-sync-rework]] - datahub-assignment-injector rework decision
- [[shipit-adr15-user-sync-mapping]] - Kafka topic to Synchronizer action mapping (ADR15)
- [[DatahubAssignmentInjector]] - upstream Kafka Streams component that populates DataHub user tables
