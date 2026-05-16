---
title: "Analysis: Moving Partner Client Configuration from Parameter Store to Database"
type: source
tags: [source, routing, security, encryption, credentials]
sources:
  - "[raw/routing/Analysis Moving Partner Client Configuration From Parameter Store To Database - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166060753/Analysis+Moving+Partner+Client+Configuration+From+Parameter+Store+To+Database)"
last_updated: 2026-05-16
---

## Summary

This analysis proposes storing partner OAuth2 credentials (clientId, clientSecret, tokenUrl) encrypted in the Route Finder PostgreSQL database instead of a centralized parameter store. Credentials are stored as `bytea` columns encrypted via PostgreSQL's pgcrypto extension using AES-256 (pgp_sym_encrypt/pgp_sym_decrypt). The encryption key is stored separately from the database - either in a protected file, a Java Keystore (JKS), or as an environment variable - to ensure that compromising the database alone is insufficient to expose credentials. This "separation of concerns" approach requires two simultaneous compromises (DB + key file) to expose credentials.

## Key Claims

- AES-256 symmetric encryption via PostgreSQL pgcrypto chosen; RSA-2048 asymmetric considered but rejected (slower, more complex).
- Credentials stored in existing `routing.shpartnerconfig` table as encrypted `bytea` columns.
- JPA repository uses native queries with `pgp_sym_decrypt()` to return decrypted values.
- PartnerCredentialEntity exposes only decrypted fields: clientId, clientSecret, tokenUrl.
- Encryption key must never be stored in: the same database, application code, logs, heap dumps, error traces.
- Threat model: SQL injection or DB backup theft alone cannot expose credentials; attacker must also compromise key storage.
- Environment variable is acceptable for containerized/offline deployments where HSM is unavailable.
- Application loads decrypted credentials into in-memory cache at startup; cache is kept only in memory.

## Evidence and Notes

- Encryption: pgp_sym_encrypt() / pgp_sym_decrypt() with AES-256; FIPS 140-2 compliant; pgcrypto extension available in Amazon Aurora PostgreSQL.
- Key management standards: NIST SP 800-57, PCI DSS (when applicable), Principle of Least Privilege.
- Environment variable cons: visible to same-user processes, may appear in `ps auxe`, often logged in CI systems.
- JNDI / JKS preferred for production; environment variable acceptable for offline installations.

## Related Links

- [[RouteFinder]] - the Route Finder service being secured
- [[PartnerCredentialStorage]] - concept page for credential encryption approach
- [[ShipItOAuth2]] - OAuth2 credential usage context
- [[ApigeeKvm]] - prior credential storage approach
- [[versioned-key-rotation-vs-token-broker]] - earlier credential access analysis
