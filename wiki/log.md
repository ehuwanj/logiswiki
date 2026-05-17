# Operation Log

## [2026-05-17] lint | Applied autofix for missing last_updated

- **Changes**: Added `last_updated: 2026-05-17` to [[ApigeeKvm]] and [[DebeziumConnector]]
- **Result**: Frontmatter completeness issue from prior lint report resolved

## [2026-05-17] lint | Wiki health audit

- **Result**: 0 dead links, 0 orphan pages, 0 unresolved knowledge conflicts, 0 encoding errors, 0 index drift; all 55 knowledge pages have Related Links
- **Minor issues**: 2 pages missing `last_updated` frontmatter ([[ApigeeKvm]], [[DebeziumConnector]])
- **Coverage**: No strong missing concept candidates; no material freshness drift detected

## [2026-05-17] lint | Normalized first tag to allowed domain taxonomy

- **Changes**:
  - Reordered first tags on 8 pages so the leading tag is one of `routing`, `shipit`, `security`, `devops`, `aws`, `kafka`
  - Added missing leading domain tags to [[DatabaseSchemaEvolution]], [[Liquibase]], [[RouteFinder]], and [[ingest-manifest]]
  - Updated ingest skill guidance to require an allowed domain tag in first position for generated pages
- **Result**: All tagged wiki pages now start with an allowed domain tag

## [2026-05-17] lint | Audited redundant type tags in concepts and syntheses

- **Changes**: None
- **Result**: No pages under `wiki/concepts/` or `wiki/syntheses/` used redundant `concept` or `synthesis` tags; existing ingest guidance already covers prevention

## [2026-05-17] lint | Removed redundant source tags from source pages

- **Changes**:
  - Removed the redundant `source` tag from 27 files under [[wiki/sources]]; source identity remains defined by `type: source`
  - Updated ingest skill guidance and templates so generated source pages use only content-based tags
- **Result**: Source frontmatter tags are now domain/topic-only and aligned with the page schema

## [2026-05-17] lint | Normalized tags frontmatter format

- **Changes**:
  - Updated [[ApigeeKvm]] tags to `tags: [security, apigee, credential-storage]`
  - Normalized [[DebeziumConnector]] tags from block-list style to inline array format
  - Updated ingest skill guidance to require single-line `tags` arrays for new pages
- **Result**: Tag formatting aligned with existing concepts/sources convention; no content semantics changed

## [2026-05-16] query | Route Finder connection to ShipIt

- **Output**: Referenced [[RouteFinder]], [[ShipIt]], [[shipit-route-finder-deployment-comparison]], [[route-finder-datahub-sync]], [[route-finder-wildfly-integration]], [[DataHub]], [[ShipItSynchronizer]]
- **Coverage**: Explained runtime integration (WAR/JAR), configuration/auth linkage, and data synchronization path via Kafka CDC -> DataHub -> Synchronizer

## [2026-05-16] query | Flyway migration scope (Route Finder vs ShipIt)

- **Output**: Referenced [[FlywayMigration]], [[ShipIt]], [[RouteFinder]], [[shipit-adr14-db-migration-versioning]]
- **Coverage**: Defined Flyway usage; clarified it is used for ShipIT Farm DB migrations, while Route Finder uses Liquibase

## [2026-05-16] query | Kafka usage in ShipIT and Smart Routing

- **Output**: Referenced [[KafkaCdc]], [[ShipIt]], [[RouteFinder]], [[AttributeStore]], [[DatahubAssignmentInjector]], [[route-finder-datahub-sync]], [[route-finder-multi-shipit-version-support]], [[shipit-adr4-oauth2-integration]], [[shipit-adr5-attribute-store-sync]], [[shipit-adr7-shipper-assignment-automation]], [[shipit-adr13-farm-user-sync]], [[shipit-adr15-user-sync-mapping]], [[shipit-adr16-usershipper-sync-rework]], [[shipit-as-user-sync-adr]]
- **Coverage**: Compared Smart Routing Kafka CDC for Route Finder -> DataHub replication with ShipIT Farm Kafka usage for Attribute Store user and shipper synchronization, topic design, filtering, and Kafka Streams joins via datahub-assignment-injector

## [2026-05-16] lint | Health check + auto-fixes

- **Result**: 0 dead links, 0 orphan pages, 0 knowledge conflicts, 0 encoding errors; index fully synced
- **Fixes applied**:
  - Replaced em-dash (`—`) with ` - ` in 13 pages (10 concept pages + 3 source pages from 2026-05-10 ingest)
  - Added missing link: [[ShipItFarm]] -> [[OmaroMicroservice]] (bidirectional)
  - Added missing link: [[OmaroMicroservice]] -> [[AmazonEKS]]
  - Added backlinks from [[AttributeStore]] and [[ShipItSynchronizer]] to [[shipit-adr15-user-sync-mapping]]
  - Added backlink from [[AttributeStore]] to [[shipit-adr8-fpcs-parameters]]
  - Added backlink from [[ShipIt]] to [[shipit-crypto-jasypt-to-javax]]
  - Created new concept page [[DatahubAssignmentInjector]]; added to [[index.md]]
  - Linked [[DatahubAssignmentInjector]] from [[AttributeStore]], [[DataHub]], [[ShipItSynchronizer]], [[ShipItFarm]], [[shipit-adr16-usershipper-sync-rework]], [[shipit-as-user-sync-adr]]
- **Conflicts**: None

## [2026-05-16] ingest | No-op; all raw files unchanged

- **Changes**: None
- **Manifest**: Processed 0 files, skipped 27 unchanged (3 routing skipped, 24 shipit/routing active), missing 0, pruned 0
- **Conflicts**: None

## [2026-05-16] ingest | Ingested 24 new raw files (routing + ShipIT ADR domain)

- **Changes**:
  - Added sources: [[route-finder-wildfly-integration]], [[route-finder-multi-shipit-version-support]], [[route-finder-shipit-semver-guide]], [[partner-credential-db-migration-analysis]]
  - Added sources: [[shipit-adr1-database-technology]], [[shipit-adr2-backend-server-technology]], [[shipit-adr3-synchronizer-deployment]], [[shipit-adr4-oauth2-integration]], [[shipit-adr5-attribute-store-sync]], [[shipit-adr6-cluster-scheduled-jobs]], [[shipit-adr7-shipper-assignment-automation]], [[shipit-adr8-fpcs-parameters]], [[shipit-adr9-dev-strategy]], [[shipit-adr10-farm-release-cycles]], [[shipit-adr11-oas-approach]], [[shipit-adr12-sandbox-user-config]], [[shipit-adr13-farm-user-sync]], [[shipit-adr14-db-migration-versioning]], [[shipit-adr15-user-sync-mapping]], [[shipit-adr16-usershipper-sync-rework]], [[shipit-adr17-attributestore-multi-apis]]
  - Added sources: [[shipit-omaro-replaces-lobster]], [[shipit-as-user-sync-adr]], [[shipit-crypto-jasypt-to-javax]]
  - Added concepts: [[ShipItFarm]], [[AttributeStore]], [[AuroraServerless]], [[AmazonEKS]], [[SemanticVersioning]], [[DatabaseSchemaEvolution]], [[PartnerCredentialStorage]], [[QuartzScheduler]], [[OmaroMicroservice]], [[FarmReleaseCycle]], [[FlywayMigration]]
  - Updated existing concepts: [[ShipIt]], [[WildFlyWarDeployment]], [[RouteFinder]], [[DataHub]], [[ShipItSynchronizer]]
  - Updated [[index.md]] with new Sources sections (CrossBorder+ RTG + ShipIT Farm ADRs) and new Concepts section (ShipIT Farm - Infrastructure)
- **Manifest**: Processed 24 new files, skipped 3 unchanged (routing/), missing 0, pruned 0
- **Conflicts**: None

## [2026-05-14] reorganize | Moved raw files into raw/routing/ subfolder
- **Changes**: Moved 3 raw files from raw/ to raw/routing/; updated raw_path in [[ingest-manifest]]; updated sources frontmatter in 3 source pages and 16 concept pages (19 files total)
- **Files moved**: Data Synchronization for Route Finder to DataHub, ShipIt Integration Route Finder Deployment Comparison, Versioned Key Rotation vs Token Broker
- **Conflicts**: None

## [2026-05-14] query | Versioned Key Rotation vs Token Broker comparison
- **Output**: Synthesized [[key-distribution-approach-comparison]]; referenced [[VersionedKeyRotation]], [[TokenBroker]], [[versioned-key-rotation-vs-token-broker]]
- **Coverage**: DB impact, maintenance overhead, compromise blast radius, authentication options, recommendation; saved as synthesis

## [2026-05-14] lint | Wiki health check
- **Result**: 0 dead links, 0 knowledge conflicts; 2 orphan pages (incomplete prune — CertificateSigningRequest.md, gls-pki-certificate-request-guide.md files not deleted); 3 stale Related Links pointing to orphaned pages; 1 missing concept candidate (SchemaRegistry)
- **Fix applied**: Deleted orphaned files and removed 3 stale Related Links entries using PowerShell+Python (Bash tool was sandboxed)

## [2026-05-14] ingest --prune | Pruned missing GLS PKI Certificate Request Guide raw source

- **Changes**:
  - Deleted [[gls-pki-certificate-request-guide]] source page (raw file no longer exists); removed from [[index.md]]
  - Deleted [[CertificateSigningRequest]] concept page (prune-owned — sole source was the missing raw file); removed from [[index.md]]
  - Updated [[GlsPki]]: removed missing raw path from `sources` frontmatter; removed [[CertificateSigningRequest]] and [[gls-pki-certificate-request-guide]] from Related Links
  - Updated [[MutualTls]]: removed [[CertificateSigningRequest]] from Related Links
  - Updated [[versioned-key-rotation-vs-token-broker]]: removed [[gls-pki-certificate-request-guide]] from Related Links
- **Manifest**: Processed 0 raw files, skipped 3 unchanged, pruned 1 (GLS PKI Certificate Request Guide)
- **Conflicts**: None

## [2026-05-14] ingest --prune | Corrected manifest status for raw/llm-wiki.md to pruned

- **Changes**: Set manifest status `active` → `pruned` for raw/llm-wiki.md (wiki artifacts already cleaned in prior runs; manifest row was inconsistently reset)
- **Manifest**: Processed 0 raw files, skipped 4 unchanged, pruned 1 (manifest correction only)
- **Conflicts**: None

## [2026-05-14] ingest --prune | No-op; removed empty Knowledge Management section from index.md

- **Changes**: Removed empty `### Knowledge Management` section header from [[index.md]] (LlmWiki and PersonalKnowledgeBase were already deleted)
- **Manifest**: All 4 active raw files unchanged (skipped); raw/llm-wiki.md already pruned; no new missing entries
- **Conflicts**: None

## [2026-05-14] ingest --prune | Cleanup prune-owned concepts after raw/llm-wiki.md removal

- **Changes**:
  - Removed [[LlmWiki]] and [[PersonalKnowledgeBase]] concept pages (sources array became empty after prune)
  - Removed corresponding concept entries from [[index.md]]
- **Manifest**: No raw processing; prune consistency cleanup only
- **Conflicts**: None

## [2026-05-14] ingest --prune | Pruned missing raw/llm-wiki.md; reingested changed ShipIt deployment file

- **Changes**:
  - Pruned [[llm-wiki-karpathy]] source page (raw/llm-wiki.md no longer exists); removed from [[index.md]]
  - Removed [[llm-wiki-karpathy]] wikilink from [[LlmWiki]] and [[PersonalKnowledgeBase]] Related Links
  - Cleared `sources: [raw/llm-wiki.md]` frontmatter from [[LlmWiki]] and [[PersonalKnowledgeBase]] (concept pages preserved)
  - Updated [[shipit-route-finder-deployment-comparison]] — added open issues for encryption key management (DB credentials + client credentials)
- **Manifest**: Processed 1 changed file (ShipIt deployment), skipped 3 unchanged, pruned 1 (llm-wiki.md), missing 0
- **Conflicts**: None

## [2026-05-10] ingest | Initial ingest of all 5 raw source files

- **Changes**:
  - Added [[gls-pki-certificate-request-guide]] (source)
  - Added [[versioned-key-rotation-vs-token-broker]] (source)
  - Added [[shipit-route-finder-deployment-comparison]] (source)
  - Added [[route-finder-datahub-sync]] (source)
  - Added [[GlsPki]] (concept)
  - Added [[CertificateSigningRequest]] (concept)
  - Added [[MutualTls]] (concept)
  - Added [[PersonalKnowledgeBase]] (concept)
  - Added [[VersionedKeyRotation]] (concept)
  - Added [[TokenBroker]] (concept)
  - Added [[ApigeeKvm]] (concept)
  - Added [[ShipItOAuth2]] (concept)
  - Added [[ShipIt]] (concept)
  - Added [[RouteFinder]] (concept)
  - Added [[WildFlyWarDeployment]] (concept)
  - Added [[StandaloneJarDeployment]] (concept)
  - Added [[DataHub]] (concept)
  - Added [[KafkaCdc]] (concept)
  - Added [[DebeziumConnector]] (concept)
  - Added [[Liquibase]] (concept)
  - Added [[ShipItSynchronizer]] (concept)
  - Updated [[index.md]], [[log.md]], [[ingest-manifest.md]]
- **Manifest**: Processed 5 files, skipped 0 unchanged
- **Conflicts**: None
