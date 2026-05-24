---
title: "ShipIT Farm"
type: concept
tags: [shipit, farm, kubernetes, eks, cloud, multi-tenant]
sources:
  - "[raw/shipit/ADR1 Database technology - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200844181/ADR1+Database+technology)"
  - "[raw/shipit/ADR2 Backend server technology - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200844191/ADR2+Backend+server+technology)"
  - "[raw/shipit/ADR4 Integrate OAuth 2.0 authentication in ShipIT Farm - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200903369/ADR4+Integrate+OAuth+2.0+authentication+in+ShipIT+Farm)"
last_updated: 2026-05-16
---

## Definition

The ShipIT Farm is a cloud-hosted, multi-tenant deployment of the ShipIT backend running on AWS infrastructure. Unlike traditional ShipIT installations (local/on-premise or central webbackend), the Farm runs multiple stateless backend instances sharing a single Aurora Serverless v2 PostgreSQL database, orchestrated on Amazon EKS. It is accessed via Apigee with OAuth 2.0 authentication (not basic auth), and users are managed via the Attribute Store microservice (GPP project).

## Key Information

**Architecture:**
- Multiple stateless WildFly backend instances sharing one Aurora Serverless v2 database (ADR1)
- Hosted on Amazon EKS (ADR2); deployed in a separate namespace with Istio service mesh
- Apigee serves as the API gateway; OAuth 2.0 required (no basic auth in Farm mode)
- Two logical Farm instances per environment: `main` (production) and `sandbox` (test)

**User Management:**
- Users managed in Attribute Store (GPP team), not in ShipIT frontend
- User assignments synced to Farm via Kafka (Debezium CDC from AS -> datahub-assignment-injector -> DataHub -> Synchronizer -> backend)
- Shipper assignments auto-derived from user-shipper assignments in Attribute Store (no manual Control Center step)

**Scheduled Jobs:**
- Non-parallelizable jobs run on a single separate pod (never externally reachable)
- Frontend-only jobs (backups, updates, etc.) disabled for Farm mode (ADR6)

**Development Strategy:**
- Farm codebase merged back into fpcs repository (ADR9)
- Farm releases more frequently than classic ShipIT; version format: `X.Y-fN[-rcM]` (ADR10)
- Farm-first features: backend/Synchronizer only, no DataHub or other service changes required

**Database:**
- Aurora Serverless v2 with PostgreSQL compatibility; scales in ACU (charged per second); min 1 ACU recommended
- DataHub now holds user management tables: role, user, user_backend, user_backend_role, user_shipper, user_parameter

## Related Links

- [[ShipIt]] - the core ShipIT system that Farm extends
- [[AuroraServerless]] - database choice for Farm
- [[AmazonEKS]] - hosting platform
- [[AttributeStore]] - user management service
- [[DataHub]] - central data hub now holding user tables
- [[ShipItSynchronizer]] - syncs data from DataHub to backend DB
- [[QuartzScheduler]] - scheduled jobs in Farm cluster
- [[FarmReleaseCycle]] - Farm's faster release strategy
- [[shipit-adr1-database-technology]] - DB technology ADR
- [[shipit-adr2-backend-server-technology]] - hosting ADR
- [[shipit-adr4-oauth2-integration]] - OAuth2 integration ADR
- [[shipit-adr11-oas-approach]] - REST OAS design-first vs code-first ADR (TBD)
- [[OmaroMicroservice]] - Kafka-to-FTP parcel data service running on EKS
- [[DatahubAssignmentInjector]] - Kafka Streams component joining AS and DataHub topics for user sync
