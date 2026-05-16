---
title: "ShipIT ADR4: Integrate OAuth 2.0 Authentication in ShipIT Farm"
type: source
tags: [source, shipit, farm, oauth2, authentication, adr]
sources:
  - "[raw/shipit/ADR4 Integrate OAuth 2.0 authentication in ShipIT Farm - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200903369/ADR4+Integrate+OAuth+2.0+authentication+in+ShipIT+Farm)"
last_updated: 2026-05-16
---

## Summary

ShipIT backends use basic authentication, but the Farm must integrate with Apigee and GLS Developer Portal which require OAuth 2.0. This ADR defines how to add OAuth 2.0 support and how to handle the user management shift from the backend database to the Attribute Store microservice (GPP project). Decision: Option 3c - add OAuth 2.0 as an additional auth method, replicate user details to the backend database, and receive user changes via Kafka push from Attribute Store.

## Key Claims

- Decision: Option 3 + sub-option 3c: OAuth 2.0 added (not replacing basic auth), users replicated to backend DB, changes pushed via Kafka.
- Rationale: preserves backward compatibility with local/web ShipIT installations; low code impact; near-zero delay for user changes.
- Attribute Store (GPP team) provides a Kafka topic that ShipIT consumes to replicate users to its internal database.
- User table must be extended with a column holding the OAuth 2.0 OID from the token for lookup.
- Frontend endpoints will not be exposed to Farm; only REST and SOAP endpoints need OAuth 2.0.
- Option 1 (full replacement): rejected because many places in backend use User entity from DB; breaking change.
- Option 3a (lazy loading): rejected due to synchronous calls to AS on every login request.
- Option 3b (scheduled pull): rejected due to delay and dependency on new AS bulk endpoint.
- Option 3c (push via Kafka): preferred because AS already uses Kafka CDC scenarios.

## Evidence and Notes

- Current auth flows: LoginFilter (frontend token), RESTLoginFilter (basic auth), SoapLoginFilter (basic auth).
- Attribute Store managed by GPP team; holds user OID, volumetrictype, assigned shippers.
- GLS Developer Portal provides credentials for OAuth 2.0 token issuance via Apigee endpoints.
- Infinicache considered for distributed cache to reduce AS load (optional).

## Related Links

- [[ShipItFarm]] - Farm context
- [[AttributeStore]] - GPP's user management service
- [[ShipItOAuth2]] - ShipIT's OAuth2 mechanism
- [[ApigeeKvm]] - Apigee integration
- [[shipit-adr5-attribute-store-sync]] - how user data is synced via Kafka
- [[shipit-adr13-farm-user-sync]] - Farm-specific user filtering
