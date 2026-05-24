---
title: "Mutual TLS"
type: concept
tags: [security, tls, authentication, certificates, apigee]
sources: ["[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"]
last_updated: 2026-05-10
---

## Definition

Mutual TLS (mTLS) is an authentication protocol where both the client and server present X.509 certificates to verify each other's identity during a TLS handshake. Unlike standard TLS (where only the server is authenticated), mTLS also authenticates the client. Within GLS CrossBorder+, mTLS is one of two options (alongside ShipIt OAuth2) for securing the connection between on-premise Route Finder installations and Apigee API proxies.

## Key Information

- **How it works**: The client (on-premise Route Finder) presents a client certificate; the server (Apigee virtual host) validates it against a configured trust store. The server also presents its certificate, which the client validates.
- **GLS PKI integration**: Client certificates for mTLS are issued from the GLS root CA at pki.gls-group.net. The root CA and intermediate CA must be stored in the trust store of the Apigee mTLS-enabled virtual host.
- **Apigee virtual hosts**: An mTLS virtual host for dev exists at secure-api-dev.gls-group.net. Equivalent hosts for stage and prod must be created.
- **Installation**: During customer site installation, a new PKI certificate is issued per customer. The customer private key must be protected and access limited to Smart Routing and administrators.
- **Security boundary**: mTLS secures the transport channel and authenticates the caller by certificate possession. However, any runtime that controls the trusted client certificate and private key can retrieve the raw encryption key - the private key must therefore be treated as a high-sensitivity credential.
- **Comparison to ShipIt OAuth2**: mTLS relies on protection of the private key; ShipIt OAuth2 relies on protection of the client secret. Both approaches offer equivalent security at deployment and are applicable to both Solution A (Versioned Key Rotation) and Solution B (Token Broker).
- **PKI lifecycle**: Certificate issuance and revocation managed at pki.gls-group.net.

## Related Links

- [[GlsPki]] - GLS PKI issues the client certificates for mTLS
- [[ShipItOAuth2]] - alternative authentication approach to mTLS
- [[TokenBroker]] - Apigee token broker secured by mTLS or ShipIt OAuth2
- [[VersionedKeyRotation]] - Apigee key distribution API secured by mTLS or ShipIt OAuth2
- [[versioned-key-rotation-vs-token-broker]] - source document comparing mTLS vs ShipIt OAuth2
