---
title: "Partner Credential Storage"
type: concept
tags: [security, encryption, credentials, postgresql, pgcrypto]
sources:
  - "[raw/routing/Analysis Moving Partner Client Configuration From Parameter Store To Database - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060753/Analysis+Moving+Partner+Client+Configuration+From+Parameter+Store+To+Database)"
last_updated: 2026-05-16
---

## Definition

Partner Credential Storage is the approach for storing partner OAuth2 credentials (clientId, clientSecret, tokenUrl) encrypted within the Route Finder PostgreSQL database using AES-256 symmetric encryption via the PostgreSQL pgcrypto extension. The encryption key is stored separately (JKS, environment variable, or protected file) following the separation-of-concerns security principle.

## Key Information

**Storage Design:**
- Table: `routing.shpartnerconfig` (extended with credential columns)
- Column type: `bytea` (encrypted)
- Encryption: `pgp_sym_encrypt()` / `pgp_sym_decrypt()` with AES-256
- JPA repository uses native queries with `pgp_sym_decrypt()` to return decrypted values
- Entity exposes: clientId, clientSecret, tokenUrl (no plaintext columns in DB)
- Credentials loaded into in-memory cache at application startup

**Key Management:**
- Key stored separately from the database (not in the same DB, not in code)
- Options: protected file on application server, Java Keystore (JKS), or environment variable
- Standards: NIST SP 800-57, PCI DSS, Principle of Least Privilege
- Environment variable: acceptable for containerized/offline deployments; weaker than HSM/JKS

**Threat Model:**
- SQL injection: only encrypted bytea exposed (useless without key)
- DB backup stolen: useless without key
- Insider with DB access: needs both DB + key file access
- Key file breach: needs DB access to decrypt (defense in depth)

**Rules:**
- Never hardcode the encryption key in application code
- Never store the key in the same database as encrypted data
- Disable SQL parameter logging for queries involving the encryption key
- Never write the key to logs, heap dumps, error traces, or exception messages

## Related Links

- [[RouteFinder]] - application storing and using partner credentials
- [[ShipItOAuth2]] - OAuth2 tokens obtained using these credentials
- [[ApigeeKvm]] - prior credential storage approach in Apigee
- [[partner-credential-db-migration-analysis]] - analysis document
- [[versioned-key-rotation-vs-token-broker]] - earlier credential access design
