---
title: "Versioned Key Rotation vs Token Broker"
type: source
tags: [security, api, encryption, shipit, crossborder-plus]
sources: ["[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"]
last_updated: 2026-05-10
---

## Summary

This DRAFT design document (ISRS-23427, under Epic ISRS-19336) compares two approaches for securely distributing partner API credentials when Smart Routing (Route Finder) is integrated with ShipIt on-premise. On-premise deployments cannot use AWS Parameter Store, so an alternative key distribution mechanism is required. Solution A uses versioned key rotation via an Apigee API proxy; Solution B uses a token broker proxy that retrieves short-lived tokens centrally. The preferred recommendation is **Solution B (Token Broker) with ShipIt OAuth2 authentication**.

## Key Claims

- On-premise ShipIt installations cannot reach AWS Parameter Store, so the encryption key for partner credentials must be distributed by another mechanism.
- Solution A (Versioned Key Rotation): encryption key stored in Apigee KVM, a no-target API proxy at `/partner-key/v1` returns it; DB columns `key_version` and `key_url` added to SHPARTNERCONFIG; versioned rotation with ~3-month grace period.
- Solution B (Token Broker): client ID, client secret, and token URL stored in Apigee KVM; a reverse proxy at `/token-broker/v1` calls partner token URL and returns the short-lived token; only `token_broker_url` column needed in SHPARTNERCONFIG; zero ongoing maintenance.
- Both solutions can use either mTLS or ShipIt OAuth2 for transport-layer authentication.
- Token Broker has zero maintenance effort; Versioned Key Rotation requires medium maintenance (re-encryption, version management, overlap periods).
- If credential is compromised: token broker exposure is limited to the short-lived token's validity period; key rotation requires key revocation, re-encryption of all partner credentials, and DB/config synchronization across all customer sites.
- Preferred solution: **Token Broker + ShipIt OAuth2** (option 4 in the document).

## Evidence and Notes

- OWASP design principles applied: unwrapped key never persisted at customer site; key in memory only; operational simplicity required.
- Apigee KVM entries use encryption at rest; values not visible in plaintext in UI.
- mTLS requires PKI certificates from GLS root CA (pki.gls-group.net); Apigee mTLS virtual host currently exists for dev (secure-api-dev.gls-group.net); stage and prod hosts need creation.
- ShipIt OAuth2 reuses the existing OAuth2 token pool and requires adding `OAuthV2 VerifyAccessToken` policy to Apigee ProxyEndpoint PreFlow.
- Cache-Control: no-store and Pragma headers should be set on both proxy responses to minimize caching risk.

## Related Links

- [[VersionedKeyRotation]] - encryption key versioning approach
- [[TokenBroker]] - Apigee token broker pattern
- [[ApigeeKvm]] - Apigee Key Value Maps for credential storage
- [[MutualTls]] - mTLS authentication pattern
- [[ShipItOAuth2]] - ShipIt OAuth2 authentication
- [[RouteFinder]] - Route Finder (RTG) component
- [[ShipIt]] - ShipIt on-premise shipping system
- [[GlsPki]] - GLS PKI for client certificate issuance
