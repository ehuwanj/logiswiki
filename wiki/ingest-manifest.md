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
| raw/devops/Github Actions - Onboarding Applications Guide - CrossBorder+.md | 38410 / 2026-05-24T14:54:31 | 2026-05-24 | wiki/sources/github-actions-onboarding-guide.md | active |
| raw/devops/Github Actions - Blueprint Workflow Definition - CrossBorder+.md | 6083 / 2026-05-24T14:59:17 | 2026-05-24 | wiki/sources/github-actions-blueprint-workflow.md | active |
| raw/devops/Github Action - Java Gradle  Docker build and push - CrossBorder+.md | 8001 / 2026-05-24T15:00:18 | 2026-05-24 | wiki/sources/github-actions-gradle-docker-push.md | active |
| raw/devops/Github Actions Build Workflow - CrossBorder+.md | 31920 / 2026-05-24T15:00:52 | 2026-05-24 | wiki/sources/github-actions-build-workflow-concept.md | active |
| raw/devops/Github Actions Build Workflow - CrossBorder+ 1.md | missing | 2026-05-24 | wiki/sources/github-actions-build-workflow-concept.md | missing |
| raw/devops/Github Actions Reusable Workflow Versioning - CrossBorder+.md | 15102 / 2026-05-24T15:02:22 | 2026-05-24 | wiki/sources/github-actions-reusable-workflow-versioning.md | active |
| raw/devops/GitHub Actions Workflow Dynamic Elements - CrossBorder+.md | 9925 / 2026-05-24T15:03:25 | 2026-05-24 | wiki/sources/github-actions-workflow-dynamic-elements.md | active |
| raw/routing/Analysis Unique Tour Management - CrossBorder+.md | 3476 / 2026-05-24T15:14:50 | 2026-05-24 | wiki/sources/analysis-unique-tour-management.md | active |
| raw/routing/Analysis Long Haul Routing - CrossBorder+.md | 8594 / 2026-05-24T15:16:44 | 2026-05-24 | wiki/sources/analysis-long-haul-routing.md | active |
| raw/routing/Business Object Model Leg Based Routing - CrossBorder+.md | 20555 / 2026-05-24T15:17:24 | 2026-05-24 | wiki/sources/business-object-model-leg-based-routing.md | active |
| raw/routing/Network for leg based routing - CrossBorder+.md | 24637 / 2026-05-24T15:18:20 | 2026-05-24 | wiki/sources/network-leg-based-routing.md | active |
| raw/routing/Shipper Specific Routing - CrossBorder+.md | 15378 / 2026-05-24T15:59:23 | 2026-05-24 | wiki/sources/shipper-specific-routing-overview.md | active |
| raw/routing/Long Haul Routing - National - International - Partners - CrossBorder+.md | 3262 / 2026-05-24T15:20:14 | 2026-05-24 | wiki/sources/long-haul-routing-national-international-partners.md | active |
| raw/routing/Shipper Specific Routing in Route-Finder - CrossBorder+.md | 5132 / 2026-05-24T15:21:39 | 2026-05-24 | wiki/sources/shipper-specific-routing-route-finder.md | active |
| raw/routing/Definition and Evaluation of Short Haul Routing Rule Sets - CrossBorder+.md | 5843 / 2026-05-24T15:22:35 | 2026-05-24 | wiki/sources/definition-evaluation-short-haul-routing-rule-sets.md | active |
| raw/routing/Nearest zipcode match (comparison between Unique, ShipIt and RTG) - CrossBorder+.md | 21648 / 2026-05-24T15:23:35 | 2026-05-24 | wiki/sources/nearest-zipcode-match-comparison.md | active |
| raw/routing/Nemonic Codes - CrossBorder+.md | 4117 / 2026-05-24T15:23:40 | 2026-05-24 | wiki/sources/nemonic-codes-routing.md | active |
| raw/routing/Partner Pre-selection - CrossBorder+.md | 2420 / 2026-05-24T15:25:32 | 2026-05-24 | wiki/sources/partner-preselection.md | active |
| raw/routing/Data Modell Routing Solution - CrossBorder+.md | 16338 / 2026-05-24T15:25:55 | 2026-05-24 | wiki/sources/data-model-routing-solution.md | active |
| raw/routing/Definition of Rules - How can logic be modelled in rules - CrossBorder+.md | 23563 / 2026-05-24T15:27:42 | 2026-05-24 | wiki/sources/definition-of-rules-modeling-logic.md | active |
| raw/routing/Migration Concept - CrossBorder+.md | 3332 / 2026-05-24T15:29:45 | 2026-05-24 | wiki/sources/migration-concept-routing.md | active |
| raw/2026-05-24.md | 0 / 2026-05-24T15:30:32 | 2026-05-24 |  | active |
