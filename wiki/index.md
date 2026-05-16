# Wiki Index

## Sources

### CrossBorder+ Smart Routing

- [[versioned-key-rotation-vs-token-broker]] - design comparison of two approaches to securely distribute partner API credentials to on-premise ShipIt; recommends Token Broker + ShipIt OAuth2.
- [[shipit-route-finder-deployment-comparison]] - deployment analysis for Route Finder with ShipIt on-premise (WAR vs JAR); recommends standalone JAR deployment.
- [[route-finder-datahub-sync]] - design for synchronizing Route Finder database changes to ShipIt DataHub via Kafka CDC; Avro + Debezium preferred; strict additive-only schema evolution required.
- [[route-finder-wildfly-integration]] - WildFly 38 integration analysis; WAR build steps (bootWar, jboss-deployment-structure.xml, RouteFinderServletInitializer); Jakarta EE 10 compatibility.
- [[route-finder-multi-shipit-version-support]] - analysis of supporting 3 concurrent ShipIT versions on one RTG DB; additive-only schema evolution; Kafka topic versioning.
- [[route-finder-shipit-semver-guide]] - SemVer 2.0.0 guide for Route Finder and ShipIT; ShipIT_X.Y.Z branches; Nexus immutability; axion-release plugin.
- [[partner-credential-db-migration-analysis]] - analysis of moving partner OAuth2 credentials from Parameter Store to PostgreSQL with AES-256/pgcrypto; key management options.

### ShipIT Farm ADRs

- [[shipit-adr1-database-technology]] - ADR1: Aurora Serverless v2 chosen for ShipIT Farm database.
- [[shipit-adr2-backend-server-technology]] - ADR2: Amazon EKS chosen for ShipIT Farm backend hosting.
- [[shipit-adr3-synchronizer-deployment]] - ADR3: Synchronizer deployed as independent EKS application for Farm.
- [[shipit-adr4-oauth2-integration]] - ADR4: OAuth2 Option 3c selected; add OAuth2 to Farm + push users from control center via Kafka.
- [[shipit-adr5-attribute-store-sync]] - ADR5: separate Kafka topics (Option 2) chosen for Attribute Store sync to Farm.
- [[shipit-adr6-cluster-scheduled-jobs]] - ADR6: single-node separate pod (Option 1) for non-parallelizable Quartz jobs in Farm cluster.
- [[shipit-adr7-shipper-assignment-automation]] - ADR7: auto-fill shipper_backend from AS Kafka topic (Option 3); no manual maintenance.
- [[shipit-adr8-fpcs-parameters]] - ADR8: user-specific FPCS parameters stored per user_backend (Option 2).
- [[shipit-adr9-dev-strategy]] - ADR9: merge Farm and classic codebases (Option 3); multi-step validation approach.
- [[shipit-adr10-farm-release-cycles]] - ADR10: dev-farm branch + X.Y-fN version format for faster Farm releases.
- [[shipit-adr11-oas-approach]] - ADR11: OpenAPI Specification approach (design-first vs code-first); TBD.
- [[shipit-adr12-sandbox-user-config]] - ADR12: main vs sandbox Farm controlled by farm_id in user_role table.
- [[shipit-adr13-farm-user-sync]] - ADR13: initial AS REST load + Kafka updates (Option 2) for Farm user sync.
- [[shipit-adr14-db-migration-versioning]] - ADR14: uniform SQL filename notation (Option 5) satisfying both Flyway and Install4J updater.
- [[shipit-adr15-user-sync-mapping]] - ADR15: Kafka topic to Synchronizer action mapping table for user sync.
- [[shipit-adr16-usershipper-sync-rework]] - ADR16: datahub-assignment-injector replaces as-data-provider + shipper-assignment-injector.
- [[shipit-adr17-attributestore-multi-apis]] - ADR17: split Attribute Store into ShipIT/GPP-specific APIs; 5 options evaluated; TBD.
- [[shipit-omaro-replaces-lobster]] - ShipIT-ADR1: Omaro microservice replaces Lobster_data for parcel data to Unique via TRN/FTP.
- [[shipit-as-user-sync-adr]] - ShipIT-ADR2: datahub-assignment-injector design; Kafka Streams joining AS and DataHub topics.
- [[shipit-crypto-jasypt-to-javax]] - ShipIT-ADR3: replace jasypt/BouncyCastle with javax.crypto for ShipIT encryption.

## Concepts

### GLS Infrastructure & Security

- [[GlsPki]] - GLS Group Public Key Infrastructure at pki.gls-group.net; issues internal certificates from GLS root CA.
- [[MutualTls]] - mutual TLS authentication pattern; used to secure Apigee API proxies; requires GLS PKI client certificates.
- [[ApigeeKvm]] - Apigee Key Value Maps; encrypted runtime storage for credentials and keys used by API proxies.

### CrossBorder+ Smart Routing - Security

- [[VersionedKeyRotation]] - encryption key versioning approach; distributes partner API encryption key via Apigee proxy; medium maintenance overhead.
- [[TokenBroker]] - preferred approach; Apigee reverse proxy exchanges partner credentials for short-lived tokens; zero maintenance.
- [[ShipItOAuth2]] - ShipIt existing OAuth2 service reused to authenticate Route Finder against Apigee proxies; preferred authentication approach.
- [[PartnerCredentialStorage]] - AES-256/pgcrypto in PostgreSQL for partner OAuth2 credentials; key stored separately from DB.

### CrossBorder+ Smart Routing - System Components

- [[ShipIt]] - GLS shipping system with WildFly backend, Synchronizer, and DataHub; extended for Route Finder integration and Farm cloud variant.
- [[RouteFinder]] - Smart Routing (RTG) component providing routing decisions via REST API; Spring Boot 3.5.7 / Jakarta EE 10; supports 3 concurrent ShipIT versions.
- [[WildFlyWarDeployment]] - WAR deployment for Route Finder inside ShipIt WildFly 38; requires bootWar + jboss-deployment-structure.xml.
- [[StandaloneJarDeployment]] - recommended JAR deployment model for Route Finder as independent Windows service.
- [[DataHub]] - ShipIt central data distribution hub; holds routing data and Farm user/shipper management tables.
- [[ShipItSynchronizer]] - Spring Boot app; consumes DataHub data and pushes to backend; runs in EKS for Farm.
- [[SemanticVersioning]] - SemVer 2.0.0 for Route Finder and ShipIT; ShipIT_X.Y.Z branches; Nexus immutability; axion-release plugin.

### CrossBorder+ Smart Routing - Data Synchronization

- [[KafkaCdc]] - Kafka Change Data Capture pipeline (Debezium -> Kafka -> JDBC sink -> DataHub); preferred data sync approach.
- [[DebeziumConnector]] - CDC source connector that reads RTG PostgreSQL WAL and publishes changes to Kafka topics.
- [[Liquibase]] - Route Finder database migration framework; must produce versioned SQL files compatible with ShipIt Component Updater.
- [[DatabaseSchemaEvolution]] - additive-only schema rules for multi-version ShipIT support; deprecation before removal; Kafka topic versioning.

### ShipIT Farm - Infrastructure

- [[ShipItFarm]] - GLS-controlled cloud ShipIT on Amazon EKS + Aurora Serverless v2; OAuth2; Attribute Store; Quartz cluster; main/sandbox modes.
- [[AuroraServerless]] - AWS Aurora Serverless v2 PostgreSQL-compatible DB chosen for ShipIT Farm; ACU-based auto-scaling; min 1 ACU recommended.
- [[AmazonEKS]] - Managed Kubernetes on existing GLS cluster; separate namespace with Istio; chosen for ShipIT Farm backend.
- [[AttributeStore]] - GPP team user management microservice; Debezium CDC -> Kafka; syncs users/shipper assignments to Farm via datahub-assignment-injector.
- [[FarmReleaseCycle]] - X.Y-fN[-rcM] version format; dev-farm branch; farm-first features; faster than classic ShipIT cycle.
- [[FlywayMigration]] - DB migration tool for ShipIT Farm; runs during K8s pod start; uniform SQL filename notation satisfying both Flyway and Install4J.
- [[QuartzScheduler]] - 20+ periodic jobs in ShipIT; Farm uses single-node pod for non-parallelizable DB-writing jobs.
- [[OmaroMicroservice]] - replaces Lobster_data platform; Kafka -> TRN file -> FTP for parcel data to Unique; runs on EKS.
- [[DatahubAssignmentInjector]] - Kafka Streams app joining AS and DataHub Debezium topics; writes to DataHub user management tables; replaces as-data-provider + shipper-assignment-injector.

## Syntheses

- [[key-distribution-approach-comparison]] - side-by-side comparison of Versioned Key Rotation vs Token Broker; covers DB impact, maintenance, and compromise response; recommends Token Broker + ShipIt OAuth2.
