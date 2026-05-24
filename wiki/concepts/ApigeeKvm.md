---
title: "Apigee KVM"
type: concept
tags: [security, apigee, credential-storage]
sources:
  - "[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"
last_updated: 2026-05-17
---

## Definition

Apigee Key Value Maps (KVMs) are a runtime configuration storage mechanism within Apigee API Management. They store key-value pairs that API proxies can read at runtime via the `KeyValueMapOperations` policy. KVMs support encryption at rest, making them suitable for storing sensitive values such as API credentials, encryption keys, and tokens.

## Key Information

- **Encryption at rest**: KVMs can be created with encryption enabled; values are not visible in plaintext in the Apigee management UI.
- **Access pattern**: API proxies read KVM values at runtime using `KeyValueMapOperations` policy with GET operation; values are stored in private flow variables to prevent accidental exposure.
- **Use in CrossBorder+**:
  - *Versioned Key Rotation*: Stores encryption key per version (e.g., `PARTNER_ENCRYPTION_KEY_V1`, `PARTNER_ENCRYPTION_KEY_V2`).
  - *Token Broker*: Stores partner client ID, client secret, and token URL.
- **Security**: Values stored in KVM are only accessible to API proxies in the same Apigee environment; not exposed to external callers directly.
- **No-target proxy pattern**: For key distribution, a no-target Apigee proxy reads the KVM and returns the value as the HTTP response - the request is handled entirely within Apigee without hitting a backend.
- **Environments**: KVM entries exist per Apigee environment (dev, stage, prod); separate entries are configured per environment.

## Related Links

- [[VersionedKeyRotation]] - uses Apigee KVM to store versioned encryption keys
- [[TokenBroker]] - uses Apigee KVM to store partner OAuth2 credentials
- [[MutualTls]] - authentication used to protect KVM-backed API proxies
- [[ShipItOAuth2]] - alternative authentication for KVM-backed proxies
- [[versioned-key-rotation-vs-token-broker]] - source document describing KVM usage
