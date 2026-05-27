---
title: "GLS PKI Certificate Renewal User Guide"
type: source
tags: [security, pki, certificates]
sources:
  - "[raw/security/2. GLS PKI Certificate Renewal User Guide - Information Security Germany.md](https://gls-group.atlassian.net/wiki/spaces/ISG/pages/138281437/2.+GLS+PKI+Certificate+Renewal+User+Guide)"
last_updated: 2026-05-27
---

## Summary

Guide for renewing a GLS PKI certificate before it expires. Renewal is only permitted once the certificate validity falls at or below the 60-day default threshold. The process re-uses the same certificate request workflow for the same Subject Name, ensuring no duplicate valid certificates with overlapping validity exist.

## Key Claims

- The default renewal window is 60 days: a certificate cannot be renewed while its remaining validity is above 60 days.
- PKI sends automated reminder emails to the certificate owner when validity falls below 30 days.
- Renewal process = follow the standard Certificate Request Guide for the identical Subject Name.
- PKI enforces uniqueness: it does not allow two valid certificates with the same Subject Name and validity above 60 days simultaneously.

## Evidence and Notes

- To find certificates approaching expiry: Home > My Certificates shows history including active and expired certificates.
- The requester must note the Subject Name of the certificate before initiating renewal.
- Portal: pki.gls-group.net (same as request).

## Related Links

- [[GlsPki]] - GLS PKI infrastructure and lifecycle overview
- [[gls-pki-certificate-request-guide]] - request process reused for renewal
- [[gls-pki-user-manual]] - parent documentation index
- [[gls-pki-certificate-revocation-guide]] - when certificate must be invalidated before expiry
