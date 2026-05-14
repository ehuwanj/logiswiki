---
title: "ShipIt OAuth2"
type: concept
tags: [security, oauth2, authentication, shipit, crossborder-plus]
sources: ["[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"]
last_updated: 2026-05-10
---

## Definition

ShipIt OAuth2 is the OAuth 2.0 authentication service already embedded in the ShipIt ecosystem for farm/cloud, classical, and on-premise deployments. It is used to authenticate the ShipIt backend and synchronizer against the control center. Within CrossBorder+ Smart Routing, it is proposed as the preferred authentication mechanism for on-premise Route Finder to access Apigee API proxies (key distribution API or token broker).

## Key Information

- **Existing usage**: ShipIt OAuth2 is already operational in all ShipIt deployment modes (farm/cloud, classical, on-premise). During installation, a unique backend name and installation token are generated, encrypted with a hardcoded salt, and stored in the Synchronizer config. On startup, the Synchronizer exchanges credentials for an access token used for all outbound API calls.
- **Integration steps for Route Finder**:
  1. ShipIt installer enhanced to write the unique backend name and installation token to the Route Finder application config during installation.
  2. Route Finder reads and decrypts the installation token at startup.
  3. Route Finder uses the token to authenticate against the Apigee key distribution or token broker API.
  4. In Apigee, the proxy is configured with an OAuthV2 `VerifyAccessToken` policy in the PreFlow to validate ShipIt OAuth2 tokens.
- **Apigee policy snippet**:
  ```xml
  <OAuthV2 name="Verify-ShipIt-OAuth2-Token">
    <Operation>VerifyAccessToken</Operation>
  </OAuthV2>
  ```
- **Security model**: Relies on protection of the client secret (installation token). The same token pool is shared with ShipIt, so security improvements to ShipIt OAuth2 automatically apply to Smart Routing.
- **Advantage over mTLS**: No client certificate provisioning or trust store configuration required; reuses existing ShipIt infrastructure.
- **Preferred**: ShipIt OAuth2 is the preferred authentication approach (lower deployment effort) in the token broker solution.

## Related Links

- [[TokenBroker]] — uses ShipIt OAuth2 as the preferred authentication approach
- [[VersionedKeyRotation]] — can also use ShipIt OAuth2 for the key distribution API
- [[MutualTls]] — alternative authentication approach
- [[ShipIt]] — system that owns and operates the OAuth2 service
- [[versioned-key-rotation-vs-token-broker]] — source document describing ShipIt OAuth2 integration
