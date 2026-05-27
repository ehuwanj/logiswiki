---
title: "GLS PKI Certificate Request User Guide"
type: source
tags: [security, pki, certificates]
sources:
  - "[raw/security/1. GLS PKI Certificate Request User Guide - Information Security Germany.md](https://gls-group.atlassian.net/wiki/spaces/ISG/pages/138281517/1.+GLS+PKI+Certificate+Request+User+Guide)"
last_updated: 2026-05-27
---

## Summary

Step-by-step guide for requesting a GLS PKI certificate via the production portal at pki.gls-group.net. Supports two request paths: uploading an externally generated CSR (PKCS10 format) or generating the key pair directly on the PKI system. Both paths require RA Officer approval and result in a downloadable certificate.

## Key Claims

- The requester must navigate to pki.gls-group.net and select the correct realm (e.g., "GLS PKI Internal Certificate") from the landing page.
- Two certificate request options are available: "CSR Upload" (requester holds the private key) and "Generate Key on PKI" (PKI generates and stores the key pair).
- A mandatory "Department Email" (team group mailbox or team member alternate address) must be provided for all certificate requests.
- The FQDN/hostname must be registered in the GLS network; requests that fail DNS Policy check may be rejected by the RA Officer (Policy Exception with justification is possible).
- For "Generate Key on PKI", a keystore password is set at request time; the keystore cannot be downloaded if the password is lost.
- After submission, a PKI workflow ID is generated and notification emails are sent to the requester and RA Officer.
- Approved certificates are available via Home > My Certificates and also delivered by email.

## Evidence and Notes

- CSR Upload path: requester uploads PKCS10 file; CSR details auto-populate; requester verifies Hostname/FQDN.
- Generate Key on PKI path: PKI creates the key pair; keystore is password-protected; downloadable in multiple formats via "Action" in My Certificates.
- Workflow status is trackable under Home > My workflows.

## Related Links

- [[GlsPki]] - GLS PKI infrastructure and governance
- [[gls-pki-user-manual]] - parent documentation index
- [[gls-pki-certificate-renewal-guide]] - renewal process after certificate expiry approaches
- [[CertificateSigningRequest]] - PKCS10 format used in the CSR Upload path
- [[MutualTls]] - mTLS usage context for GLS PKI certificates
