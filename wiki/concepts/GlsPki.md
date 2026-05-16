---
title: "GLS PKI"
type: concept
tags: [pki, security, certificates, gls-infrastructure]
sources: ["[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"]
last_updated: 2026-05-10
---

## Definition

The GLS Group Public Key Infrastructure (PKI) is the internal certificate authority infrastructure operated by GLS Information Security Germany. It issues certificates from a GLS root CA and is accessible via the production portal at pki.gls-group.net. It is used to issue TLS/mTLS client certificates for internal services, including Apigee virtual hosts in on-premise and cloud deployments.

## Key Information

- **Production portal**: pki.gls-group.net
- **Root CA**: GLS root CA (with issuing intermediate CAs stored in trust stores)
- **Realms**: The landing page presents multiple realms (e.g., "GLS Group Service CA", "GLS PKI Internal Certificate"). Each realm has its own home page and certificate profiles.
- **Governance**: Controlled by PKI RA Officers who review and approve/reject certificate requests. Requests are subject to DNS Policy checks (FQDN must be registered in GLS network).
- **Certificate request process**:
  1. Select realm → Request Certificate → choose certificate profile
  2. Upload CSR (PKCS10) or Generate Key on PKI
  3. Provide FQDN/hostname and optional SANs
  4. Provide mandatory Department Email (group mailbox preferred)
  5. Pass DNS Policy check (or submit policy exception with justification)
  6. Submit → get workflow ID → RA Officer approval → download certificate
- **Key management**: Requester must hold private key. For Generate Key on PKI option, a keystore password is required at download time and cannot be recovered if lost.
- **Certificate lifecycle**: Issuance and revocation managed via the portal. Certificates downloadable from Home → My Certificates.
- **mTLS usage**: GLS PKI certificates are recommended for Apigee mTLS virtual hosts (e.g., secure-api-dev.gls-group.net). During customer site installation, a new PKI certificate should be issued, and the GLS root CA plus intermediate CA stored in the Apigee trust store.
- **User policy**: PKI User Policy document governs Do's and Don'ts before certificate requests.

## Related Links

- [[MutualTls]] - mTLS authentication using GLS PKI certificates
- [[versioned-key-rotation-vs-token-broker]] - recommends GLS PKI for Apigee mTLS
