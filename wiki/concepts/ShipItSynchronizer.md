---
title: "ShipIt Synchronizer"
type: concept
tags: [shipit, synchronizer, data-sync, datahub, spring-boot]
sources: ["[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)", "[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"]
last_updated: 2026-05-10
---

## Definition

The ShipIt Synchronizer is a Spring Boot application deployed alongside each ShipIt backend instance. It consumes messages from Kafka topics (via Data Provider and Parameter Provider components) and applies corresponding data updates to the ShipIt backend database. It is the final step in the Route Finder data synchronization pipeline: DataHub DB → Synchronizer → customer backend DB.

## Key Information

- **Architecture**: Spring Boot application, one per ShipIt backend instance. Deployed at each customer site alongside the ShipIt backend.
- **Data flow role**: Reads routing data from the DataHub database and synchronizes it to the local ShipIt backend database. This makes Route Finder routing data available to the on-premise instance.
- **Version-aware**: Each ShipIt version's Synchronizer reads only the tables and columns compatible with its supported Route Finder version. This allows the DataHub to hold data for multiple ShipIt versions simultaneously without issues.
- **OAuth2 integration**: Uses ShipIt OAuth2 to authenticate against the control center. On startup, exchanges credentials (backend name + installation token) for an access token used for outbound API calls.
- **Kafka consumption**: The Synchronizer consumes messages from Kafka topics via Data Provider and Parameter Provider. During the Route Finder integration, it must be extended to also consume routing data changes from DataHub DB.
- **ShipIt team responsibility**: Extension of the Synchronizer for Route Finder data is the responsibility of the ShipIt team.
- **ADR reference**: Architecture described in ADR3 (Deploy Synchronizer for Webbackend Farm) and ADR16 (Rework user/shipper sync).

## Related Links

- [[DataHub]] — data source for the Synchronizer
- [[KafkaCdc]] — upstream synchronization pipeline feeding DataHub
- [[ShipIt]] — parent system that owns and deploys the Synchronizer
- [[ShipItOAuth2]] — authentication mechanism used by the Synchronizer
- [[RouteFinder]] — source of routing data being synchronized
- [[route-finder-datahub-sync]] — source document describing Synchronizer's role
