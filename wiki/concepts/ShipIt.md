---
title: "ShipIt"
type: concept
tags: [shipit, gls, logistics, on-premise, shipping-system, crossborder-plus, farm, eks, aurora]
sources:
  - "[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"
  - "[raw/routing/ShipIt Integration Route Finder Deployment Comparison - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166064255/ShipIt+Integration+Route+Finder+Deployment+Comparison)"
  - "[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)"
  - "[raw/shipit/ADR1 ShipIT Farm - Database technology - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200836918/ADR1+ShipIT+Farm+-+Database+technology)"
  - "[raw/shipit/ADR2 ShipIT Farm - Backend server technology - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200837012/ADR2+ShipIT+Farm+-+Backend+server+technology)"
  - "[raw/shipit/ADR4 OAuth2 Integration - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200840474/ADR4+OAuth2+Integration)"
  - "[raw/shipit/ADR10 Shorter Release Cycles for FARM - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200848792/ADR10+Shorter+Release+Cycles+for+FARM)"
last_updated: 2026-05-16
---

## Definition

ShipIt is a GLS shipping management system that runs in multiple deployment modes: ShipIT Farm (cloud, GLS-controlled Amazon EKS), classical (customer-hosted), and on-premise at customer sites. It consists of a backend (WildFly servlet container), a frontend, and a Synchronizer component. The Farm variant runs on Aurora Serverless v2, uses OAuth 2.0 for authentication, and synchronizes user/shipper data from the Attribute Store via Kafka. ShipIt is being extended (Epic ISRS-19336) to integrate the Route Finder (RTG) component for Smart Routing capabilities.

## Key Information

**Classic / On-Premise:**
- **Download and installation**: Available via shipit-download.gls-group.eu (credentials required). Installs as a Windows service on Windows OS (Linux also supported).
- **Default install path**: `gls\shipit_backend\` and `gls\shipit_frontend\`
- **Configuration**: `gls\shipit_backend\config\configuration.properties` - stores WildFly, database, and Route Finder REST API base URL settings.
- **Application server**: WildFly servlet container. Current version: WildFly 26; upgrade to WildFly 38 planned for ShipIt 6.x (before October 2026), enabling WAR deployment of Route Finder.
- **Authentication**: Uses OAuth 2.0 (ShipIt OAuth2) between backend/synchronizer and control center. Unique backend name and encrypted installation token generated per installation.
- **DB migrations**: SQL files applied by Install4J updater during installation; naming convention must also satisfy Flyway (used by Farm).

**ShipIT Farm (Cloud):**
- **Infrastructure**: Amazon EKS (GLS-owned cluster, separate namespace with Istio service mesh); Aurora Serverless v2 PostgreSQL-compatible database.
- **User management**: Users and shipper assignments managed by the Attribute Store (GPP team). Synchronized to Farm via Debezium CDC -> Kafka -> datahub-assignment-injector -> DataHub tables -> Synchronizer.
- **User data tables in DataHub** (added for Farm): `role`, `user`, `user_backend`, `user_backend_role`, `user_shipper`, `user_parameter`; `shipper_backend` auto-populated from AS Kafka topic.
- **Farm release cycle**: Separate `dev-farm` branch; version format `X.Y-fN[-rcM]`; faster than classic ShipIT release cycle (ADR10).
- **Sandbox mode**: main vs. sandbox ShipIT Farm controlled by `farm_id` in user_role table (ADR12).
- **DB migrations (Farm)**: Flyway runs during K8s pod start; SQL files use `schema_update_fpcs_X.Y.Z.W__X.Y.fZ-rcW.sql` naming to satisfy both Flyway and Install4J updater (ADR14).
- **Scheduled jobs**: Non-parallelizable DB-writing jobs run on a separate single-node backend pod; parallelizable jobs run on all pods (ADR6).

**Shared:**
- **Synchronizer**: Spring Boot application deployed alongside each backend instance. Consumes routing data from DataHub and user/shipper assignments from datahub-assignment-injector output.
- **DataHub**: Central data distribution hub. Routing data from the RTG database is synchronized to DataHub DB via Kafka CDC, then distributed to customer databases by the Synchronizer.
- **Database**: Shares PostgreSQL with Route Finder (on-premise). DB passwords encrypted by the installer.
- **Versioning**: Three major ShipIt versions of Route Finder must be supported simultaneously on a single RTG database. DB schema evolution must be additive.
- **Route Finder integration**: Deployment options: WAR in WildFly or standalone JAR (recommended for on-premise).

## Related Links

- [[ShipItFarm]] - Farm cloud variant on EKS + Aurora Serverless v2
- [[RouteFinder]] - Route Finder component being integrated into ShipIt
- [[ShipItOAuth2]] - ShipIt OAuth2 authentication service
- [[DataHub]] - ShipIt data distribution hub
- [[ShipItSynchronizer]] - Synchronizer component for data synchronization
- [[AttributeStore]] - GPP user management service syncing to Farm
- [[AuroraServerless]] - Farm database technology
- [[AmazonEKS]] - Farm backend hosting
- [[FarmReleaseCycle]] - Farm release versioning strategy
- [[FlywayMigration]] - Farm DB migration tool
- [[QuartzScheduler]] - Farm scheduled job clustering strategy
- [[WildFlyWarDeployment]] - WAR deployment option for on-premise
- [[StandaloneJarDeployment]] - recommended JAR deployment option
- [[shipit-route-finder-deployment-comparison]] - deployment comparison source
- [[route-finder-datahub-sync]] - data synchronization approach
- [[versioned-key-rotation-vs-token-broker]] - credential management for on-premise
- [[shipit-adr1-database-technology]] - Farm DB technology decision
- [[shipit-adr2-backend-server-technology]] - Farm backend hosting decision
- [[shipit-adr10-farm-release-cycles]] - Farm release cycle decision
- [[shipit-crypto-jasypt-to-javax]] - jasypt to javax.crypto encryption migration
- [[UniQue]] - legacy backbone delegating-routing relationship
- [[NemonicCode]] - label code emitted by Route Finder, consumed by ShipIt
- [[NearestZipcodeMatch]] - delegates routing/zipcode handling to UniQue
- [[Eircode]] - replicates Irish virtual codes locally
- [[nearest-zipcode-match-comparison]] - zipcode behavior comparison source
- [[nemonic-codes-routing]] - Spain label code source
