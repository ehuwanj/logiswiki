---
title: "Certificate Signing Request"
type: concept
tags: [security, pki, certificates]
sources:
  - "[raw/security/1. GLS PKI Certificate Request User Guide - Information Security Germany.md](https://gls-group.atlassian.net/wiki/spaces/ISG/pages/138281517/1.+GLS+PKI+Certificate+Request+User+Guide)"
last_updated: 2026-05-27
---

## Definition

A Certificate Signing Request (CSR) is a structured message in PKCS10 format that a certificate applicant sends to a Certificate Authority (CA) to request a signed certificate. It contains the public key and certificate parameters (such as the FQDN/hostname and optional Subject Alternative Names), and is signed with the applicant's private key to prove possession of it.

## Key Information

- **Format**: PKCS10 (standard X.509 CSR encoding).
- **Private key requirement**: The requester must hold the associated private key. The private key is never submitted - only the public key is included in the CSR.
- **Contents**: FQDN/hostname (mandatory), Subject Alternative Names and port (optional).
- **GLS PKI usage**: In the GLS PKI portal (pki.gls-group.net), the "CSR Upload" request path accepts a PKCS10 file. Once uploaded, the portal auto-populates the certificate details for review. The requester then verifies the FQDN and provides a mandatory Department Email before submitting the workflow.
- **Alternative path**: "Generate Key on PKI" - the PKI system generates the key pair internally. In this case no CSR is submitted; instead the requester sets a keystore password. The CSR Upload path is preferred when the requester needs to control the private key.
- **DNS policy check**: After CSR upload, the FQDN must pass a DNS policy check (must be registered in the GLS network). Failure may result in rejection by the RA Officer unless a Policy Exception with justification is submitted.

## Related Links

- [[GlsPki]] - GLS PKI infrastructure; the CA that processes CSR submissions
- [[gls-pki-certificate-request-guide]] - step-by-step procedure for submitting a CSR at pki.gls-group.net
- [[MutualTls]] - mTLS certificates obtained via the CSR-based request process
