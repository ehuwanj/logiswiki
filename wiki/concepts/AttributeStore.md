---
title: "Attribute Store"
type: concept
tags: [attribute-store, gpp, user-management, kafka, microservice]
sources:
  - "[raw/shipit/ADR4 Integrate OAuth 2.0 authentication in ShipIT Farm - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200903369/ADR4+Integrate+OAuth+2.0+authentication+in+ShipIT+Farm)"
  - "[raw/shipit/ADR5 Sync Attribute Store users to Farm - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200842845/ADR5+Sync+Attribute+Store+users+to+Farm)"
  - "[raw/shipit/ADR13 Only sync Farm users from attribute-store - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200846022/ADR13+Only+sync+Farm+users+from+attribute-store)"
last_updated: 2026-05-16
---

## Definition

The Attribute Store is a microservice introduced by the GPP project that manages user identity and authorization information for multiple GLS applications including ShipIT Farm and GPP. It stores user profiles, shipper assignments, roles, and user parameters. It exposes both a REST API and Kafka topics (via Debezium CDC) for downstream consumers.

## Key Information

**Data Managed:**
- User records: OID (Azure AD object ID), active state, volumetrictype
- Role assignments: user_oid, role_name, role_system (FARM/GPP), farm_id (main/sandbox)
- Shipper assignments: user_oid <-> shipper contact_id mapping
- User parameters: key-value settings per user

**Kafka Topics (public, farm-facing):**
- `shipit_attributestore_user` - user master data (internal IDs stripped)
- `shipit_attributestore_user-shipper` - user-shipper assignments (using OID + contact_id)
- Additional: user-role topics

**Integration with ShipIT Farm:**
- Debezium CDC events from AS internal DB -> base topics -> public enriched topics -> datahub-assignment-injector -> DataHub -> Synchronizer -> Farm backend DB
- `user_role` topic is the "leading topic": new farm_id assignment triggers initial full user load via AS REST API (ADR13)
- Initial load: if user unknown to Farm when role assigned, Synchronizer calls AS REST API for full user details

**Architecture evolution (ADR17):**
- AS currently handles both ShipIT and GPP data in one service
- ADR17 evaluates splitting GPP into its own microservice with separate DB or shared schema

**Sandbox support:**
- Users can be assigned to `main`, `sandbox`, or both (ADR12)
- `farm_id` field in user_role messages distinguishes the target Farm instance

## Related Links

- [[ShipItFarm]] - primary consumer of Attribute Store data
- [[ShipItSynchronizer]] - downstream consumer
- [[DataHub]] - intermediate store (datahub-assignment-injector)
- [[KafkaCdc]] - CDC infrastructure used by Attribute Store
- [[DebeziumConnector]] - Debezium used for AS CDC
- [[shipit-adr4-oauth2-integration]] - OAuth2 integration using AS
- [[shipit-adr5-attribute-store-sync]] - topic structure
- [[shipit-adr13-farm-user-sync]] - user filtering by farm_id
- [[shipit-adr16-usershipper-sync-rework]] - rework of sync architecture
- [[shipit-adr17-attributestore-multi-apis]] - future split into ShipIT/GPP APIs
- [[shipit-adr8-fpcs-parameters]] - FPCS parameters per user_backend (ADR8)
- [[shipit-adr15-user-sync-mapping]] - Kafka topic to Synchronizer action mapping (ADR15)
- [[DatahubAssignmentInjector]] - Kafka Streams component consuming AS topics to write DataHub tables
