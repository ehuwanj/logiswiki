# Operation Log

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
