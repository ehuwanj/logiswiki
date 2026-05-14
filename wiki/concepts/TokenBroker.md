---
title: "Token Broker"
type: concept
tags: [security, authentication, apigee, oauth2, crossborder-plus]
sources: ["[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"]
last_updated: 2026-05-10
---

## Definition

A Token Broker is an Apigee reverse proxy that holds partner API credentials (client ID, client secret, token URL) in Apigee KVM and exchanges them for a short-lived access token on behalf of on-premise clients. The client never possesses the partner credentials; it only receives a time-limited token. This is the **preferred approach** in CrossBorder+ for securing partner API access from on-premise ShipIt deployments.

## Key Information

- **Context**: Replaces the need for on-premise Route Finder to hold raw partner client credentials. Used when ShipIt is deployed at customer sites without AWS Parameter Store.
- **How it works**:
  1. Client (Route Finder) calls the token broker endpoint (e.g., `/token-broker/v1`) with mTLS or ShipIt OAuth2 authentication.
  2. Token broker reads client ID, client secret, and token URL from Apigee KVM.
  3. Token broker calls the partner token URL and obtains a short-lived access token.
  4. Token broker returns the short-lived token to the client.
  5. Client uses the short-lived token to call partner APIs directly.
- **DB changes**: Partner client ID, client secret, and token URL columns are **removed** from SHPARTNERCONFIG. A new column `token_broker_url` is added.
- **Maintenance**: **Zero** — credentials are managed centrally in Apigee KVM; no re-encryption or version rollout needed.
- **Security on compromise**: An attacker obtaining a valid short-lived token can only use it within its limited validity period. No further mitigation is required beyond waiting for expiry (or revoking if supported).
- **Preferred configuration**: Token Broker + ShipIt OAuth2 authentication (option 4 in the design document).
- **Implementation effort**: Low — requires only integration of a token broker endpoint in Route Finder.
- **Apigee configuration**: Reverse proxy at `/token-broker/v1` using KeyValueMapOperations to read credentials, AssignMessage to set target URL and retrieve token, cache-control headers to minimize caching risk.

## Related Links

- [[VersionedKeyRotation]] — alternative approach requiring more maintenance
- [[ApigeeKvm]] — Apigee Key Value Maps where credentials are stored
- [[MutualTls]] — one authentication option for the token broker endpoint
- [[ShipItOAuth2]] — preferred authentication option for the token broker endpoint
- [[RouteFinder]] — on-premise client that consumes the token broker
- [[versioned-key-rotation-vs-token-broker]] — source document with full comparison
- [[key-distribution-approach-comparison]] - synthesis comparing Token Broker vs Versioned Key Rotation
