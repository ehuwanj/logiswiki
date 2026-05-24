# Operation Log

## [2026-05-24] lint | Full health check + ASCII normalization

- **Result**: Index consistency perfect (0 dead entries, 0 unsynced, 0 miscategorized across 107 knowledge pages); 0 orphans; 0 missing Related Links; 0 unresolved conflicts; 0 mojibake; 0 stale heavily-linked pages.
- **Yellow findings**: 16 files contained non-ASCII characters (13 BOM, 18 right-arrows, 4 em-dashes, 1 en-dash); 33 dead wikilinks all inside `[[log.md]]` historical entries (references to operational files or pruned pages).
- **Fixes applied** (user confirmed):
  - Stripped U+FEFF BOM from 13 wiki pages.
  - Replaced U+2192 (->), U+2014 ( - ), U+2013 (-) per CLAUDE.md substitution table across 4 source/concept files and `[[log.md]]`.
  - Wrapped dead wikilink targets in `[[log.md]]` historical entries with inline backticks (e.g. `[[RuleBasedRouting]]`, `[[CertificateSigningRequest]]`, `[[gls-pki-certificate-request-guide]]`, `[[LlmWiki]]`, `[[PersonalKnowledgeBase]]`, `[[llm-wiki-karpathy]]`, `[[index.md]]`, `[[log.md]]`, `[[ingest-manifest]]`, `[[ingest-manifest.md]]`) so they no longer resolve as broken links.
- **Post-fix verification**: 0 non-ASCII characters across `wiki/`; 0 dead wikilinks across all files (operational + knowledge).

## [2026-05-24] lint | Health check after 2026-05-24 ingest

- **Result**: Index consistency clean (0 dead entries, 0 unsynced); 89 knowledge pages all have Related Links; 0 knowledge conflicts; 0 mojibake; 0 stale pages.
- **Yellow**: 1 orphan source page [[shipit-adr11-oas-approach]] (registered in index but no inbound concept-page links); 30 dead wikilinks in log.md are historical append-only references to pruned content (no fix required).
- **Red**: 1 dead link from a knowledge page: `wiki/concepts/LongHaulRouting.md` references missing `[[RuleBasedRouting]]`.
- **Proposed fixes** (awaiting user confirmation): (1) remove dead `[[RuleBasedRouting]]` bullet from [[LongHaulRouting]]; (2) add a `[[shipit-adr11-oas-approach]]` backlink from [[ShipIt]] / [[ShipItFarm]].
- **Fixes applied** (user confirmed): (1) removed `[[RuleBasedRouting]]` bullet from [[LongHaulRouting]]; (2) added `[[shipit-adr11-oas-approach]]` backlink under Related Links in [[ShipItFarm]]. Post-fix verification: 0 dead links from knowledge pages; orphan resolved (1 inbound concept link).

## [2026-05-24] ingest | Re-ingest Shipper Specific Routing (raw file now populated)

- **Changes**:
  - Created source page [[shipper-specific-routing-overview]] covering ISRS-14952 (current ShipIt overlay, monthly config files, RDS AWS `cellbasedrouting` DB, Business Cases 1 and 2, Smart Routing implementation plan).
  - Updated concept page [[ShipperSpecificRouting]] with: current-state ShipIt overlay model, monthly config-file shape, distribution path (Sven Hock -> Christian Kuhn -> GeoAPI team), data evaluation snapshot (66 contacts, 698 locations), Business Case 1 example (Fuhrmann Einzelhandel, R46/R50), Business Case 2 example (expondo, R80 bundle of ~85 finals), Viking Office rule example with OSC R87, and 4-step Smart Routing implementation plan.
  - Updated `[[index.md]]` with the new source entry.
  - Updated manifest row for `raw/routing/Shipper Specific Routing - CrossBorder+.md`: fingerprint `15378 / 2026-05-24T15:59:23`, source_page `wiki/sources/shipper-specific-routing-overview.md`, status `active`.
- **Manifest**: Processed 1 file (the previously empty file now populated), skipped 27 unchanged, missing 1 (the deleted duplicate from prior run remains missing), pruned 0.
- **Conflicts**: None. The new source extends the Route Finder design source ([[shipper-specific-routing-route-finder]]) with the business-process and current-state context; both sources back the same concept page now.

## [2026-05-24] ingest | Detect deleted duplicate raw file

- **Changes**: Detected `raw/devops/Github Actions Build Workflow - CrossBorder+ 1.md` no longer exists.
  - Marked its manifest row as `missing` (fingerprint also set to `missing`).
  - Removed the corresponding entry from the `sources` array in [[github-actions-build-workflow-concept]] (the other raw path remains; the wiki page is retained).
- **Manifest**: Processed 0 files, skipped 27 unchanged, missing 1 (the deleted duplicate), pruned 0.
- **Conflicts**: None.
- **Notes**: This was a non-destructive ingest detection; no `--prune` was requested, so the wiki source page was kept (still backed by the remaining non-duplicate raw file).

## [2026-05-24] ingest | DevOps GitHub Actions + Smart Routing rule-/leg-based design

- **Changes**: Added 19 source pages, 31 concept pages.
  - DevOps sources: [[github-actions-onboarding-guide]], [[github-actions-blueprint-workflow]], [[github-actions-build-workflow-concept]], [[github-actions-gradle-docker-push]], [[github-actions-reusable-workflow-versioning]], [[github-actions-workflow-dynamic-elements]].
  - DevOps concepts: [[GithubActions]], [[ReusableWorkflow]], [[CompositeAction]], [[XbpDevopsTools]], [[XbpReusableCi]], [[SonarQube]], [[SonatypeNexus]], [[AwsEcr]], [[Jib]], [[SemanticReleaseTool]].
  - Routing sources: [[analysis-long-haul-routing]], [[analysis-unique-tour-management]], [[definition-evaluation-short-haul-routing-rule-sets]], [[business-object-model-leg-based-routing]], [[network-leg-based-routing]], [[long-haul-routing-national-international-partners]], [[definition-of-rules-modeling-logic]], [[data-model-routing-solution]], [[migration-concept-routing]], [[shipper-specific-routing-route-finder]], [[nemonic-codes-routing]], [[partner-preselection]], [[nearest-zipcode-match-comparison]].
  - Routing concepts: [[LongHaulRouting]], [[ShortHaulRouting]], [[LegBasedRouting]], [[RoutingRule]], [[RoutingRuleSet]], [[RoutingRuleSetEnvelope]], [[RuleVersioning]], [[TourLeg]], [[LineHaul]], [[Neo4jRouting]], [[ShipperSpecificRouting]], [[PartnerPreselection]], [[NemonicCode]], [[OutboundSortingFlag]], [[NearestZipcodeMatch]], [[TBZIPFORMAT]], [[Eircode]], [[TourManagement]], [[RoutingMatrix]], [[UniQue]], [[MdmLocations]].
  - Updated [[RouteFinder]], [[ShipIt]], [[SemanticVersioning]] with cross-domain links.
  - Updated `[[index.md]]` with new "CrossBorder+ Smart Routing - Routing Logic" sources, "DevOps - GitHub Actions" sources, and matching concept categories.
- **Manifest**: Processed 21 new files (devops: 7 incl. 1 duplicate file; routing: 14 design/analysis), skipped 28 unchanged, 2 empty raw files marked active.
- **Conflicts**: None. Two raw files were 0 bytes (`raw/2026-05-24.md` and `raw/routing/Shipper Specific Routing - CrossBorder+.md`) and were recorded as active in the manifest but produced no wiki content.
- **Notes**: `Github Actions Build Workflow - CrossBorder+ 1.md` is a byte-identical duplicate of `Github Actions Build Workflow - CrossBorder+.md`; both raw paths are recorded in the manifest and both point to the same source page [[github-actions-build-workflow-concept]] (its `sources` array lists both raw paths).

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
  - Replaced em-dash (` - `) with ` - ` in 13 pages (10 concept pages + 3 source pages from 2026-05-10 ingest)
  - Added missing link: [[ShipItFarm]] -> [[OmaroMicroservice]] (bidirectional)
  - Added missing link: [[OmaroMicroservice]] -> [[AmazonEKS]]
  - Added backlinks from [[AttributeStore]] and [[ShipItSynchronizer]] to [[shipit-adr15-user-sync-mapping]]
  - Added backlink from [[AttributeStore]] to [[shipit-adr8-fpcs-parameters]]
  - Added backlink from [[ShipIt]] to [[shipit-crypto-jasypt-to-javax]]
  - Created new concept page [[DatahubAssignmentInjector]]; added to `[[index.md]]`
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
  - Updated `[[index.md]]` with new Sources sections (CrossBorder+ RTG + ShipIT Farm ADRs) and new Concepts section (ShipIT Farm - Infrastructure)
- **Manifest**: Processed 24 new files, skipped 3 unchanged (routing/), missing 0, pruned 0
- **Conflicts**: None

## [2026-05-14] reorganize | Moved raw files into raw/routing/ subfolder
- **Changes**: Moved 3 raw files from raw/ to raw/routing/; updated raw_path in `[[ingest-manifest]]`; updated sources frontmatter in 3 source pages and 16 concept pages (19 files total)
- **Files moved**: Data Synchronization for Route Finder to DataHub, ShipIt Integration Route Finder Deployment Comparison, Versioned Key Rotation vs Token Broker
- **Conflicts**: None

## [2026-05-14] query | Versioned Key Rotation vs Token Broker comparison
- **Output**: Synthesized [[key-distribution-approach-comparison]]; referenced [[VersionedKeyRotation]], [[TokenBroker]], [[versioned-key-rotation-vs-token-broker]]
- **Coverage**: DB impact, maintenance overhead, compromise blast radius, authentication options, recommendation; saved as synthesis

## [2026-05-14] lint | Wiki health check
- **Result**: 0 dead links, 0 knowledge conflicts; 2 orphan pages (incomplete prune  -  CertificateSigningRequest.md, gls-pki-certificate-request-guide.md files not deleted); 3 stale Related Links pointing to orphaned pages; 1 missing concept candidate (SchemaRegistry)
- **Fix applied**: Deleted orphaned files and removed 3 stale Related Links entries using PowerShell+Python (Bash tool was sandboxed)

## [2026-05-14] ingest --prune | Pruned missing GLS PKI Certificate Request Guide raw source

- **Changes**:
  - Deleted `[[gls-pki-certificate-request-guide]]` source page (raw file no longer exists); removed from `[[index.md]]`
  - Deleted `[[CertificateSigningRequest]]` concept page (prune-owned  -  sole source was the missing raw file); removed from `[[index.md]]`
  - Updated [[GlsPki]]: removed missing raw path from `sources` frontmatter; removed `[[CertificateSigningRequest]]` and `[[gls-pki-certificate-request-guide]]` from Related Links
  - Updated [[MutualTls]]: removed `[[CertificateSigningRequest]]` from Related Links
  - Updated [[versioned-key-rotation-vs-token-broker]]: removed `[[gls-pki-certificate-request-guide]]` from Related Links
- **Manifest**: Processed 0 raw files, skipped 3 unchanged, pruned 1 (GLS PKI Certificate Request Guide)
- **Conflicts**: None

## [2026-05-14] ingest --prune | Corrected manifest status for raw/llm-wiki.md to pruned

- **Changes**: Set manifest status `active` -> `pruned` for raw/llm-wiki.md (wiki artifacts already cleaned in prior runs; manifest row was inconsistently reset)
- **Manifest**: Processed 0 raw files, skipped 4 unchanged, pruned 1 (manifest correction only)
- **Conflicts**: None

## [2026-05-14] ingest --prune | No-op; removed empty Knowledge Management section from index.md

- **Changes**: Removed empty `### Knowledge Management` section header from `[[index.md]]` (LlmWiki and PersonalKnowledgeBase were already deleted)
- **Manifest**: All 4 active raw files unchanged (skipped); raw/llm-wiki.md already pruned; no new missing entries
- **Conflicts**: None

## [2026-05-14] ingest --prune | Cleanup prune-owned concepts after raw/llm-wiki.md removal

- **Changes**:
  - Removed `[[LlmWiki]]` and `[[PersonalKnowledgeBase]]` concept pages (sources array became empty after prune)
  - Removed corresponding concept entries from `[[index.md]]`
- **Manifest**: No raw processing; prune consistency cleanup only
- **Conflicts**: None

## [2026-05-14] ingest --prune | Pruned missing raw/llm-wiki.md; reingested changed ShipIt deployment file

- **Changes**:
  - Pruned `[[llm-wiki-karpathy]]` source page (raw/llm-wiki.md no longer exists); removed from `[[index.md]]`
  - Removed `[[llm-wiki-karpathy]]` wikilink from `[[LlmWiki]]` and `[[PersonalKnowledgeBase]]` Related Links
  - Cleared `sources: [raw/llm-wiki.md]` frontmatter from `[[LlmWiki]]` and `[[PersonalKnowledgeBase]]` (concept pages preserved)
  - Updated [[shipit-route-finder-deployment-comparison]]  -  added open issues for encryption key management (DB credentials + client credentials)
- **Manifest**: Processed 1 changed file (ShipIt deployment), skipped 3 unchanged, pruned 1 (llm-wiki.md), missing 0
- **Conflicts**: None

## [2026-05-10] ingest | Initial ingest of all 5 raw source files

- **Changes**:
  - Added `[[gls-pki-certificate-request-guide]]` (source)
  - Added [[versioned-key-rotation-vs-token-broker]] (source)
  - Added [[shipit-route-finder-deployment-comparison]] (source)
  - Added [[route-finder-datahub-sync]] (source)
  - Added [[GlsPki]] (concept)
  - Added `[[CertificateSigningRequest]]` (concept)
  - Added [[MutualTls]] (concept)
  - Added `[[PersonalKnowledgeBase]]` (concept)
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
  - Updated `[[index.md]]`, `[[log.md]]`, `[[ingest-manifest.md]]`
- **Manifest**: Processed 5 files, skipped 0 unchanged
- **Conflicts**: None
