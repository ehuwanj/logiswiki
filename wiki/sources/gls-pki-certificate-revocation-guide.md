---
title: "GLS PKI Certificate Revocation User Guide"
type: source
tags: [security, pki, certificates]
sources:
  - "[raw/security/3. GLS PKI Certificate Revocation User Guide - Information Security Germany.md](https://gls-group.atlassian.net/wiki/spaces/ISG/pages/138281046/3.+GLS+PKI+Certificate+Revocation+User+Guide)"
last_updated: 2026-05-27
---

## Summary

Guide for revoking a GLS PKI certificate via the production portal. Revocation is irreversible - once revoked, a certificate is permanently invalid and cannot be reactivated. The revocation takes immediate effect on the PKI side, though the public revocation status page is updated daily at 19:00 UTC.

## Key Claims

- Revocation is triggered via pki.gls-group.net > "Revoke Certificate" (also accessible from Request > Revoke Certificate).
- The requester must provide the certificate's Subject Name or Certificate Identifier and select a Reason Code.
- Optional fields: comment and a proposed Invalidity time for future revocation.
- Once the RA Officer approves, the certificate is revoked with immediate effect.
- PKI Revocation status is reflected in the PKI Portal at 19:00 UTC daily (status display lags actual revocation).
- Revocation is irreversible: a revoked certificate cannot be reactivated or returned to active state.

## Evidence and Notes

- Notification: automatic emails sent to requester and RA Officer when the revocation workflow is submitted; final notification sent on approval.
- Certificate status can be checked post-revocation at Home > My Certificates.

## Related Links

- [[GlsPki]] - GLS PKI infrastructure and governance
- [[gls-pki-user-manual]] - parent documentation index
- [[gls-pki-certificate-renewal-guide]] - alternative to revocation when certificate is near expiry
- [[MutualTls]] - mTLS context where certificate revocation removes access
