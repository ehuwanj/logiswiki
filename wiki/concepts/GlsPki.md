---
title: "GLS PKI"
type: concept
tags: [security, pki, certificates, gls-infrastructure]
sources:
  - "[raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/452821144/Versioned+Key+Rotation+vs+Token+Broker+for+Secure+Access+to+the+Partner+API)"
  - "[raw/security/GLS PKI User Manual - Information Security Germany.md](https://gls-group.atlassian.net/wiki/spaces/ISG/pages/138281247/GLS+PKI+User+Manual)"
  - "[raw/security/1. GLS PKI Certificate Request User Guide - Information Security Germany.md](https://gls-group.atlassian.net/wiki/spaces/ISG/pages/138281517/1.+GLS+PKI+Certificate+Request+User+Guide)"
  - "[raw/security/2. GLS PKI Certificate Renewal User Guide - Information Security Germany.md](https://gls-group.atlassian.net/wiki/spaces/ISG/pages/138281437/2.+GLS+PKI+Certificate+Renewal+User+Guide)"
  - "[raw/security/3. GLS PKI Certificate Revocation User Guide - Information Security Germany.md](https://gls-group.atlassian.net/wiki/spaces/ISG/pages/138281046/3.+GLS+PKI+Certificate+Revocation+User+Guide)"
last_updated: 2026-05-27
---

## Definition

The GLS Group Public Key Infrastructure (PKI) is the internal certificate authority infrastructure operated by GLS Information Security Germany. It issues certificates from a GLS root CA and is accessible via the production portal at pki.gls-group.net. It is used to issue TLS/mTLS client certificates for internal services, including Apigee virtual hosts in on-premise and cloud deployments.

## Key Information

- **Production portal**: pki.gls-group.net
- **Root CA**: GLS root CA (with issuing intermediate CAs stored in trust stores)
- **Realms**: The landing page presents multiple realms (e.g., "GLS Group Service CA", "GLS PKI Internal Certificate"). Each realm has its own home page and certificate profiles.
- **Governance**: Controlled by PKI RA Officers who review and approve/reject certificate requests. Requests are subject to DNS Policy checks (FQDN must be registered in GLS network).
- **Certificate request process**:
  1. Select realm -> Request Certificate -> choose certificate profile
  2. Upload CSR (PKCS10) or Generate Key on PKI
  3. Provide FQDN/hostname and optional SANs
  4. Provide mandatory Department Email (group mailbox preferred)
  5. Pass DNS Policy check (or submit policy exception with justification)
  6. Submit -> get workflow ID -> RA Officer approval -> download certificate
- **Key management**: Requester must hold private key. For Generate Key on PKI option, a keystore password is required at download time and cannot be recovered if lost.
- **Certificate lifecycle**: Issuance, renewal, and revocation managed via the portal. Certificates downloadable from Home -> My Certificates.
- **Renewal rules**: Renewal is only permitted when remaining validity is at or below 60 days. Automated reminder emails are sent when validity drops below 30 days. Renewal reuses the standard request process for the same Subject Name; PKI does not allow two valid certificates with the same Subject Name and >60 days validity simultaneously.
- **Revocation**: Initiated via Revoke Certificate; requester provides Subject Name or Certificate Identifier plus a Reason Code. Revocation takes immediate effect on the PKI side. The portal status display is updated at 19:00 UTC daily. Revocation is irreversible.
- **mTLS usage**: GLS PKI certificates are recommended for Apigee mTLS virtual hosts (e.g., secure-api-dev.gls-group.net). During customer site installation, a new PKI certificate should be issued, and the GLS root CA plus intermediate CA stored in the Apigee trust store.
- **User policy**: PKI User Policy document governs Do's and Don'ts before certificate requests.

## Related Links

- [[MutualTls]] - mTLS authentication using GLS PKI certificates
- [[versioned-key-rotation-vs-token-broker]] - recommends GLS PKI for Apigee mTLS
- [[gls-pki-user-manual]] - top-level PKI documentation index
- [[gls-pki-certificate-request-guide]] - step-by-step certificate request procedure
- [[CertificateSigningRequest]] - PKCS10 format used in the CSR Upload request path
- [[gls-pki-certificate-renewal-guide]] - renewal window rules and process
- [[gls-pki-certificate-revocation-guide]] - revocation workflow and irreversibility
