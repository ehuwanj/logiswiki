---
title: "Versioned Key Rotation"
type: concept
tags: [security, encryption, key-management, apigee, crossborder-plus]
sources: ["[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"]
last_updated: 2026-05-10
---

## Definition

Versioned Key Rotation is a security pattern for managing encryption keys across distributed customer deployments where a central key distribution API serves versioned encryption keys. Each key version is identified by a version number and a version-specific URL. When a key is compromised, a new version is published and old credentials are re-encrypted, with a grace period allowing gradual customer database synchronization.

## Key Information

- **Context**: Used in CrossBorder+ Smart Routing (Route Finder) to distribute the encryption key for partner API credentials stored in SHPARTNERCONFIG when on-premise ShipIt customers cannot access AWS Parameter Store.
- **Key storage**: In Apigee KVM with encryption at rest. Named entries per version: `PARTNER_ENCRYPTION_KEY_V1`, `PARTNER_ENCRYPTION_KEY_V2`, etc.
- **Key distribution API**: A no-target Apigee proxy at `/partner-key/v1` (version-specific path) reads the KVM entry and returns the key. Protected by mTLS or ShipIt OAuth2.
- **DB changes**: `SHPARTNERCONFIG` table gets two new columns: `key_version` (integer) and `key_url` (URL of the version-specific endpoint).
- **Key history table**: Tracks version, URL, valid_from, valid_to, status (active/deprecated/expired). Supports operational tracking and controlled lifecycle.
- **Rotation workflow**: New key in KVM -> new version-specific URL -> re-encrypt all partner credentials -> synchronize to DataHub DB and customer DBs.
- **Grace period**: ~3 months of overlap during which both old and new key endpoints remain valid, allowing gradual customer synchronization. After grace period, old key endpoint is disabled.
- **Compromise response**: Rotate key and publish new API immediately -> disable old API -> re-encrypt credentials -> synchronize across customers.
- **Maintenance effort**: Medium - requires key version management, re-encryption, API version rollout, overlap-period management, and DB synchronization.
- **Security note**: Any caller with a valid client certificate or OAuth2 token can retrieve the raw encryption key; the distribution API must be treated as highly sensitive.

## Related Links

- [[TokenBroker]] - alternative approach with zero maintenance overhead
- [[ApigeeKvm]] - Apigee Key Value Maps where the key is stored
- [[MutualTls]] - one of two authentication options for the key distribution API
- [[ShipItOAuth2]] - alternative authentication option (preferred)
- [[ShipIt]] - on-premise system that consumes the key
- [[RouteFinder]] - uses the encryption key to decrypt partner credentials
- [[versioned-key-rotation-vs-token-broker]] - source document comparing both approaches
- [[key-distribution-approach-comparison]] - synthesis comparing Versioned Key Rotation vs Token Broker
