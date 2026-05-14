---
title: "ingest-manifest"
type: source
tags: [operations, ingest-manifest]
sources: [wiki/log.md]
last_updated: 2026-05-10
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

| raw_path                                                                                          | fingerprint                 | last_ingested | source_page                                               | status |
| ------------------------------------------------------------------------------------------------- | --------------------------- | ------------- | --------------------------------------------------------- | ------ |
| raw/1. GLS PKI Certificate Request User Guide - Information Security Germany.md                   | missing                     | 2026-05-14    | wiki/sources/gls-pki-certificate-request-guide.md         | pruned |
| raw/llm-wiki.md                                                                                   | missing                     | 2026-05-14    | wiki/sources/llm-wiki-karpathy.md                         | pruned |
| raw/routing/Versioned Key Rotation vs Token Broker for Secure Access to the Partner API - CrossBorder+.md | 24659 / 2026-05-10T13:03:20 | 2026-05-10    | wiki/sources/versioned-key-rotation-vs-token-broker.md    | active |
| raw/routing/ShipIt Integration Route Finder Deployment Comparison - CrossBorder+.md                       | 13924 / 2026-05-10T20:51:11 | 2026-05-14    | wiki/sources/shipit-route-finder-deployment-comparison.md | active |
| raw/routing/Data Synchronization for Route Finder to DataHub - CrossBorder+.md                            | 15584 / 2026-05-10T20:38:08 | 2026-05-10    | wiki/sources/route-finder-datahub-sync.md                 | active |
