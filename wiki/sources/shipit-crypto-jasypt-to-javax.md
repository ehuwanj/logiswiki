---
title: "ShipIT ADR: Cryptography - Replace jasypt with javax.crypto"
type: source
tags: [source, shipit, cryptography, security, jasypt, bouncycastle, adr]
sources:
  - "[raw/shipit/ShipIt-ADR3 Cryptography - replace jasypt with javax.crypto - ShipIT.md](https://gls-group.atlassian.net/wiki/spaces/FPCS/pages/200838377/ADR3+Cryptography+-+replace+jasypt+with+javax.crypto)"
last_updated: 2026-05-16
---

## Summary

ShipIT uses jasypt and BouncyCastle for password encryption (new admin user creation, password reset, backend upgrades). A vulnerability in BouncyCastle v1.77 was discovered (GS-7325). Upgrading to v1.80 introduced an incompatibility that broke decryption between old and new versions. Decision: replace jasypt + BouncyCastle with javax.crypto (JDK 17 built-in), which fixes the vulnerability and removes the third-party dependency.

## Key Claims

- Decision: replace jasypt and BouncyCastle with javax.crypto from JDK 17 distribution.
- Vulnerability: BouncyCastle v1.77 had a known security vulnerability (GS-7325).
- Incompatibility: v1.77 and v1.80 BouncyCastle cannot interoperate for decryption (bc-java GitHub issue 1985).
- Encryption is done in DataHub; decryption in the backends and updater.
- Disadvantage: old backend instances (pre-fix) retain the vulnerability in their updater component until migrated.
- Constraint: new `backend-manager` microservice must be available (early stages of development).

## Evidence and Notes

- GS-7325: vulnerability detection in BouncyCastle v1.77.
- GS-7649: incompatibility discovered when upgrading to v1.80.
- GS-7660: tracks the javax.crypto migration.
- GS-7146: related ticket.
- Reference: OWASP Cryptographic Storage Cheat Sheet.
- Affected components: datahub, backend, installers, updater.

## Related Links

- [[ShipIt]] - ShipIT backend and datahub
- [[DataHub]] - encrypts passwords
