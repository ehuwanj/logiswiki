---
title: "Route Finder"
type: concept
tags: [route-finder, rtg, smart-routing, crossborder-plus, gls]
sources: ["[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)", "[raw/routing/ShipIt Integration Route Finder Deployment Comparison - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166064255/ShipIt+Integration+Route+Finder+Deployment+Comparison)", "[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)"]
last_updated: 2026-05-10
---

## Definition

Route Finder (also called RTG, Real-Time Gateway, or Smart Routing) is a GLS CrossBorder+ component that provides routing decision capabilities via a REST API. It is being integrated into ShipIt for on-premise deployments under Epic ISRS-19336, requiring significant adaptation to remove AWS dependencies and align with ShipIt's installer, database, and credential management approaches.

## Key Information

- **REST API**: Exposes routing decisions via a versioned REST API endpoint.
  - WAR deployment URL: `https://127.0.0.1:8443/rtg/routefinder/v1/`
  - JAR deployment URL: `https://127.0.0.1:8444/routefinder/v1/`
- **Database**: Uses PostgreSQL with two application schemas (`routing`, `rtguniqueimport`) and a `cron` schema for scheduled jobs. Database migrations managed by Liquibase (two projects: xbp-rtg-rtg-liquibase and xbp-rtg-rtg-unique-import).
- **AWS dependency**: Currently reads settings from AWS Parameter Store (DB credentials, partner client credentials). This is unavailable in on-premise deployments; settings must move to DB or config files.
- **Deployment options for on-premise**:
  - **WAR on WildFly**: Deployed inside ShipIt's WildFly container. Blocked by version mismatch (Route Finder needs WildFly 38; ShipIt uses WildFly 26).
  - **Standalone JAR** (recommended): Runs as independent Windows service at `gls\shipit_backend\rtg\`. Fully independent upgrades; new HTTPS port and keystore required.
- **Credential management**: Partner client ID, client secret, and token URL currently stored in SHPARTNERCONFIG (encrypted with encryption key). On-premise approach: move to Token Broker (preferred) or Versioned Key Rotation.
- **DB password encryption**: Must use the same encryption algorithm as the ShipIt installer to decrypt shared database credentials.
- **Versioning**: Three major ShipIt versions of Route Finder must be supported simultaneously on one RTG database in AWS.
- **Configuration**: Environment variables set via `standalone.conf.bat` (WAR) or `startRouteFinder.bat` (JAR). Includes DB_URL, DB_USERNAME, DB_PASSWORD, PARTNER API settings, ENV, LOGLEVEL.
- **Logging**: JAR deployment should write to `C:\gls\shipit_backend\log\rtg\route-finder.log`.

## Related Links

- [[ShipIt]] — system being integrated with Route Finder
- [[WildFlyWarDeployment]] — WAR deployment model
- [[StandaloneJarDeployment]] — recommended JAR deployment model
- [[TokenBroker]] — preferred credential management approach for on-premise
- [[VersionedKeyRotation]] — alternative credential management approach
- [[DataHub]] — receives synchronized Route Finder data
- [[KafkaCdc]] — mechanism for syncing Route Finder data to DataHub
- [[Liquibase]] — Route Finder database migration framework
- [[shipit-route-finder-deployment-comparison]] — deployment analysis source
- [[route-finder-datahub-sync]] — data synchronization source
- [[versioned-key-rotation-vs-token-broker]] — credential management source
