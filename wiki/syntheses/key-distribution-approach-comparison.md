---
title: "Versioned Key Rotation vs Token Broker: Partner API Credential Distribution Tradeoffs"
type: synthesis
tags: [security, apigee, key-management, crossborder-plus, shipit]
sources:
  - wiki/concepts/VersionedKeyRotation.md
  - wiki/concepts/TokenBroker.md
  - wiki/sources/versioned-key-rotation-vs-token-broker.md
last_updated: 2026-05-14
---

## Summary

Both [[VersionedKeyRotation]] and [[TokenBroker]] solve the same underlying problem: on-premise [[ShipIt]] deployments cannot reach AWS Parameter Store, so partner API credentials must be distributed via an alternative Apigee-hosted mechanism. They differ fundamentally in what they give the client and who bears the operational burden. The wiki recommendation is **Token Broker + ShipIt OAuth2** (option 4).

## What the Client Receives

[[VersionedKeyRotation]] gives the on-premise [[RouteFinder]] a **raw encryption key**. Route Finder uses that key to decrypt partner credentials (client ID, client secret) stored in its own local `SHPARTNERCONFIG` database table. The partner credentials therefore live on-premise - just encrypted.

[[TokenBroker]] gives Route Finder a **short-lived access token** directly. Partner credentials (client ID, client secret, token URL) never leave [[ApigeeKvm]]. The on-premise side only ever holds a token valid for a limited window.

## Database Impact

| | Versioned Key Rotation | Token Broker |
|---|---|---|
| Columns added | `key_version`, `key_url` | `token_broker_url` |
| Columns removed | none | client ID, client secret, token URL |
| Partner secrets on-premise? | Yes (encrypted) | No |

## Maintenance Overhead

[[VersionedKeyRotation]] carries **medium** ongoing maintenance:
- New key version in Apigee KVM -> new version-specific API endpoint
- Re-encrypt all partner credentials in every customer DB
- ~3-month overlap grace period during which both old and new endpoints stay live
- Synchronize key history table and `SHPARTNERCONFIG` across every customer site

[[TokenBroker]] carries **zero** ongoing maintenance: credentials live centrally in Apigee KVM. Updating them requires no customer-side action.

## Security Posture on Compromise

If the distributed secret is compromised:

- **Key Rotation path**: the raw encryption key is exposed, allowing decryption of all partner credentials stored at every customer site. Response requires immediate key rotation, re-encryption of all credentials, disabling the old endpoint, and synchronized rollout across all deployments - a complex, coordinated incident.
- **Token Broker path**: only a time-limited token is exposed. Blast radius is bounded by the token's validity window; no further mitigation is needed beyond expiry.

> "An attacker obtaining a valid short-lived token can only use it within its limited validity period. No further mitigation is required beyond waiting for expiry."

## Authentication Layer

Both approaches are compatible with either [[MutualTls]] or [[ShipItOAuth2]] for transport-layer authentication. [[ShipItOAuth2]] is the preferred choice for both, as it reuses existing infrastructure without requiring new PKI certificates at each customer site.

## Recommendation

[[versioned-key-rotation-vs-token-broker]] explicitly recommends **Token Broker + ShipIt OAuth2**. The rationale: lower blast radius on compromise, zero maintenance burden, and partner credentials never leave Apigee - which aligns with OWASP principles that unwrapped keys should never be persisted at customer sites.

## Related Links

- [[VersionedKeyRotation]] - encryption key distribution approach (Solution A)
- [[TokenBroker]] - short-lived token approach (Solution B, preferred)
- [[versioned-key-rotation-vs-token-broker]] - source document with full design details
- [[ApigeeKvm]] - credential storage layer for both approaches
- [[ShipItOAuth2]] - preferred authentication for both approaches
- [[MutualTls]] - alternative authentication option
- [[RouteFinder]] - on-premise client consuming either approach
- [[ShipIt]] - on-premise system housing Route Finder
