---
title: "Route Finder"
type: concept
tags: [route-finder, rtg, smart-routing, crossborder-plus, gls, spring-boot, postgresql]
sources:
  - "[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"
  - "[raw/routing/ShipIt Integration Route Finder Deployment Comparison - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166064255/ShipIt+Integration+Route+Finder+Deployment+Comparison)"
  - "[raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060827/Data+Synchronization+for+Route+Finder+to+DataHub)"
  - "[raw/routing/Analysis Adapting RTG to Support Multiple ShipIT Versions - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166061651/Analysis+Adapting+RTG+to+Support+Multiple+ShipIT+Versions)"
  - "[raw/routing/Semantic Versioning Guide for Route Finder and ShipIT - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166057945/Semantic+Versioning+Guide+for+Route+Finder+and+ShipIT)"
  - "[raw/routing/Analysis Moving Partner Client Configuration From Parameter Store To Database - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060753/Analysis+Moving+Partner+Client+Configuration+From+Parameter+Store+To+Database)"
last_updated: 2026-05-16
---

## Definition

Route Finder (also called RTG, Real-Time Gateway, or Smart Routing) is a GLS CrossBorder+ component that provides routing decision capabilities via a REST API. It is built on Spring Boot 3.5.7 / Jakarta EE 10 / Gradle and uses PostgreSQL for data storage. It is being integrated into ShipIt for on-premise deployments under Epic ISRS-19336, requiring significant adaptation to remove AWS dependencies and align with ShipIt's installer, database, and credential management approaches.

## Key Information

**REST API and deployment:**
- WAR deployment URL: `https://127.0.0.1:8443/rtg/routefinder/v1/`
- JAR deployment URL: `https://127.0.0.1:8444/routefinder/v1/`
- Tech stack: Spring Boot 3.5.7, Jakarta EE 10, Gradle, ~900 MB RAM
- **WAR on WildFly**: Requires WildFly 38 (ShipIt 6.x, planned before Oct 2026)
- **Standalone JAR** (recommended for now): Runs as independent Windows service at `gls\shipit_backend\rtg\`

**Database:**
- PostgreSQL with schemas: `routing`, `rtguniqueimport`, `cron` (scheduled jobs)
- DB migrations managed by Liquibase (two projects: xbp-rtg-rtg-liquibase, xbp-rtg-rtg-unique-import)
- **Multi-version support**: 3 concurrent ShipIT versions must be supported on one RTG database in AWS
- DB schema evolution rules: additive-only; no column drops/renames while older ShipIT versions are active; deprecation required before removal

**AWS dependency (on-premise adaptation):**
- Currently reads settings from AWS Parameter Store (DB credentials, partner client credentials)
- On-premise: settings move to DB or config files; Parameter Store removed

**Credential management (partner OAuth2):**
- Credentials: clientId, clientSecret, tokenUrl per partner
- Production (cloud): stored in `routing.shpartnerconfig` table encrypted with AES-256 via pgcrypto (`pgp_sym_encrypt`/`pgp_sym_decrypt`); key stored separately (JKS or env var)
- On-premise options: Token Broker (preferred) or Versioned Key Rotation
- Rule: never store encryption key in the same DB as encrypted data; never log the key

**Versioning:**
- Follows SemVer 2.0.0 (MAJOR.MINOR.PATCH)
- Branch naming: `ShipIT_X.Y.Z` per supported ShipIT version
- Nexus `maven-releases` repository is immutable (no overwrites allowed)
- axion-release Gradle plugin used for version management
- Kafka topic names include version suffix for schema evolution

**Configuration:**
- Environment variables set via `standalone.conf.bat` (WAR) or `startRouteFinder.bat` (JAR)
- Includes: DB_URL, DB_USERNAME, DB_PASSWORD, PARTNER API settings, ENV, LOGLEVEL
- JAR logs: `C:\gls\shipit_backend\log\rtg\route-finder.log`

## Related Links

- [[ShipIt]] - system being integrated with Route Finder
- [[WildFlyWarDeployment]] - WAR deployment model
- [[StandaloneJarDeployment]] - recommended JAR deployment model
- [[TokenBroker]] - preferred credential management approach for on-premise
- [[VersionedKeyRotation]] - alternative credential management approach
- [[PartnerCredentialStorage]] - AES-256/pgcrypto approach for cloud credential storage
- [[DatabaseSchemaEvolution]] - additive-only schema evolution rules for multi-version support
- [[SemanticVersioning]] - versioning strategy used by Route Finder
- [[DataHub]] - receives synchronized Route Finder data
- [[KafkaCdc]] - mechanism for syncing Route Finder data to DataHub
- [[Liquibase]] - Route Finder database migration framework
- [[shipit-route-finder-deployment-comparison]] - deployment analysis source
- [[route-finder-datahub-sync]] - data synchronization source
- [[versioned-key-rotation-vs-token-broker]] - credential management source
- [[route-finder-multi-shipit-version-support]] - multi-version support analysis
- [[route-finder-shipit-semver-guide]] - SemVer and branching guide
- [[partner-credential-db-migration-analysis]] - credential DB migration analysis
