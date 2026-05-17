---
title: "ingest-manifest"
type: source
tags: [devops, operations, ingest-manifest]
sources: [wiki/log.md]
last_updated: 2026-05-16
---
# Ingest Manifest

This file tracks ingest state so unchanged raw files can be skipped.

## Fields
- raw_path: relative path under raw/
- fingerprint: size + modified time (hash optional)
- last_ingested: YYYY-MM-DD
- source_page: corresponding page under wiki/sources/
- status: active | skipped | error | missing | pruned

## Prune Notes
- `missing`: raw file no longer exists; wiki content retained until prune is explicitly requested.
- `pruned`: safe cleanup completed for removed raw file (`/ingest --prune`).

## Entries

| raw_path | fingerprint | last_ingested | source_page | status |
| -------- | ----------- | ------------- | ----------- | ------ |
| raw/1. GLS PKI Certificate Request User Guide - Information Security Germany.md | missing | 2026-05-14 | wiki/sources/gls-pki-certificate-request-guide.md | pruned |
| raw/llm-wiki.md | missing | 2026-05-14 | wiki/sources/llm-wiki-karpathy.md | pruned |
| raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md | 24659 / 2026-05-10T13:03:20 | 2026-05-10 | wiki/sources/versioned-key-rotation-vs-token-broker.md | skipped |
| raw/routing/ShipIt Integration Route Finder Deployment Comparison - CrossBorder+.md | 13924 / 2026-05-10T20:51:11 | 2026-05-14 | wiki/sources/shipit-route-finder-deployment-comparison.md | skipped |
| raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md | 15584 / 2026-05-10T20:38:08 | 2026-05-10 | wiki/sources/route-finder-datahub-sync.md | skipped |
| raw/routing/Analysis Moving Partner Client Configuration From Parameter Store To Database - CrossBorder+.md | 9663 / 2026-05-16T18:06:30 | 2026-05-16 | wiki/sources/partner-credential-db-migration-analysis.md | active |
| raw/routing/Semantic Versioning Guide for Route Finder and ShipIT - CrossBorder+.md | 10692 / 2026-05-16T18:06:30 | 2026-05-16 | wiki/sources/route-finder-shipit-semver-guide.md | active |
| raw/routing/ShipIt Integration Making Route Finder ready for WildFly.md | 11844 / 2026-05-16T10:37:44 | 2026-05-16 | wiki/sources/route-finder-wildfly-integration.md | active |
| raw/routing/Support Multiple ShipIt versions in Route Finder - CrossBorder+.md | 14042 / 2026-05-16T18:06:20 | 2026-05-16 | wiki/sources/route-finder-multi-shipit-version-support.md | active |
| raw/shipit/ADR1 Database technology - ShipIT.md | 5639 / 2026-05-16T17:54:13 | 2026-05-16 | wiki/sources/shipit-adr1-database-technology.md | active |
| raw/shipit/ADR2 Backend server technology - ShipIT.md | 6838 / 2026-05-16T17:54:32 | 2026-05-16 | wiki/sources/shipit-adr2-backend-server-technology.md | active |
| raw/shipit/ADR3 Deploy Synchronizer for Webbackend Farm - ShipIT.md | 6910 / 2026-05-16T17:55:00 | 2026-05-16 | wiki/sources/shipit-adr3-synchronizer-deployment.md | active |
| raw/shipit/ADR4 Integrate OAuth 2.0 authentication in ShipIT Farm - ShipIT.md | 17464 / 2026-05-16T17:55:31 | 2026-05-16 | wiki/sources/shipit-adr4-oauth2-integration.md | active |
| raw/shipit/ADR5 Sync Attribute Store users to Farm - ShipIT.md | 9063 / 2026-05-16T17:55:43 | 2026-05-16 | wiki/sources/shipit-adr5-attribute-store-sync.md | active |
| raw/shipit/ADR6 Make scheduled jobs cluster ready - ShipIT.md | 15010 / 2026-05-16T17:55:53 | 2026-05-16 | wiki/sources/shipit-adr6-cluster-scheduled-jobs.md | active |
| raw/shipit/ADR7 Remove shipper assignments from Control Center - ShipIT.md | 7933 / 2026-05-16T17:56:29 | 2026-05-16 | wiki/sources/shipit-adr7-shipper-assignment-automation.md | active |
| raw/shipit/ADR8 Handle specific FPCS parameters without shipper context - ShipIT.md | 4112 / 2026-05-16T17:56:52 | 2026-05-16 | wiki/sources/shipit-adr8-fpcs-parameters.md | active |
| raw/shipit/ADR9 Bringing ShipIT and Farm development closer together - ShipIT.md | 14620 / 2026-05-16T17:57:07 | 2026-05-16 | wiki/sources/shipit-adr9-dev-strategy.md | active |
| raw/shipit/ADR10 Shorter Release Cycles for FARM - ShipIT.md | 9848 / 2026-05-16T17:58:08 | 2026-05-16 | wiki/sources/shipit-adr10-farm-release-cycles.md | active |
| raw/shipit/ADR11 REST OAS design or code first approach - ShipIT.md | 7468 / 2026-05-16T17:58:30 | 2026-05-16 | wiki/sources/shipit-adr11-oas-approach.md | active |
| raw/shipit/ADR12 Configuration of users in Sandbox - ShipIT.md | 3190 / 2026-05-16T17:58:49 | 2026-05-16 | wiki/sources/shipit-adr12-sandbox-user-config.md | active |
| raw/shipit/ADR13 Only sync Farm users from attribute-store - ShipIT.md | 11497 / 2026-05-16T17:59:03 | 2026-05-16 | wiki/sources/shipit-adr13-farm-user-sync.md | active |
| raw/shipit/ADR14 Manage db migrations for long and short releases - ShipIT.md | 9597 / 2026-05-16T17:59:18 | 2026-05-16 | wiki/sources/shipit-adr14-db-migration-versioning.md | active |
| raw/shipit/ADR15 User sync - which information to take from which place - ShipIT.md | 7782 / 2026-05-16T17:59:32 | 2026-05-16 | wiki/sources/shipit-adr15-user-sync-mapping.md | active |
| raw/shipit/ADR16 Rework usershipper sync - ShipIT.md | 35606 / 2026-05-16T17:59:54 | 2026-05-16 | wiki/sources/shipit-adr16-usershipper-sync-rework.md | active |
| raw/shipit/ADR17 Attributestore multi APIs - ShipIT.md | 10778 / 2026-05-16T17:59:54 | 2026-05-16 | wiki/sources/shipit-adr17-attributestore-multi-apis.md | active |
| raw/shipit/ShipIt-ADR1 Omaro to replace Lobster - ShipIT.md | 5241 / 2026-05-16T17:46:34 | 2026-05-16 | wiki/sources/shipit-omaro-replaces-lobster.md | active |
| raw/shipit/ShipIt-ADR2 AS UserShipper assignment synchronization into ShipIT - ShipIT.md | 5972 / 2026-05-16T17:46:54 | 2026-05-16 | wiki/sources/shipit-as-user-sync-adr.md | active |
| raw/shipit/ShipIt-ADR3 Cryptography - replace jasypt with javax.crypto - ShipIT.md | 3946 / 2026-05-16T17:46:54 | 2026-05-16 | wiki/sources/shipit-crypto-jasypt-to-javax.md | active |
