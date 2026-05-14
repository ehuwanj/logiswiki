---
title: "ShipIt"
type: concept
tags: [shipit, gls, logistics, on-premise, shipping-system, crossborder-plus]
sources: ["[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)", "[raw/routing/ShipIt Integration Route Finder Deployment Comparison - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166064255/ShipIt+Integration+Route+Finder+Deployment+Comparison)", "[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)"]
last_updated: 2026-05-10
---

## Definition

ShipIt is a GLS shipping management system that runs in multiple deployment modes: farm/cloud, classical, and on-premise at customer sites. It consists of a backend (WildFly servlet container), a frontend, and a Synchronizer component. ShipIt is being extended (Epic ISRS-19336) to integrate the Route Finder (RTG) component for Smart Routing capabilities.

## Key Information

- **Download and installation**: Available via shipit-download.gls-group.eu (credentials required). Installs as a Windows service on Windows OS (Linux also supported).
- **Default install path**: `gls\shipit_backend\` and `gls\shipit_frontend\`
- **Configuration**: `gls\shipit_backend\config\configuration.properties` — stores WildFly, database, and Route Finder REST API base URL settings.
- **Application server**: WildFly servlet container. Current version: WildFly 26; upgrade to WildFly 38 planned for ShipIt 6.x (before October 2026) or possibly ShipIt 5.x.
- **Authentication**: Uses OAuth 2.0 (ShipIt OAuth2) between backend/synchronizer and control center. Unique backend name and encrypted installation token generated per installation.
- **Synchronizer**: Spring Boot application deployed alongside each backend instance. Consumes Kafka topics via Data Provider and Parameter Provider to apply updates to the backend database.
- **DataHub**: Central data distribution hub. Routing data from the RTG database is synchronized to DataHub DB via Kafka CDC, and then further distributed to customer site databases by the Synchronizer.
- **Database**: Shares the PostgreSQL database with Route Finder (when deployed together). DB passwords encrypted by the installer; Route Finder must support the same decryption algorithm.
- **Versioning**: Three major ShipIt versions of Route Finder must be supported simultaneously on a single RTG database. DB schema evolution must be additive (no breaking changes while older versions are active).
- **Route Finder integration**: Route Finder (RTG) is integrated under Epic ISRS-19336. Deployment options: WAR in WildFly or standalone JAR (recommended).

## Related Links

- [[RouteFinder]] — Route Finder component being integrated into ShipIt
- [[ShipItOAuth2]] — ShipIt OAuth2 authentication service
- [[DataHub]] — ShipIt data distribution hub
- [[ShipItSynchronizer]] — Synchronizer component for data synchronization
- [[WildFlyWarDeployment]] — WAR deployment option
- [[StandaloneJarDeployment]] — recommended JAR deployment option
- [[shipit-route-finder-deployment-comparison]] — deployment comparison source
- [[route-finder-datahub-sync]] — data synchronization approach
- [[versioned-key-rotation-vs-token-broker]] — credential management for on-premise
