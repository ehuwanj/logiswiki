# Wiki Index

## Sources

- [[versioned-key-rotation-vs-token-broker]] — design comparison of two approaches to securely distribute partner API credentials to on-premise ShipIt; recommends Token Broker + ShipIt OAuth2.
- [[shipit-route-finder-deployment-comparison]] — deployment analysis for Route Finder with ShipIt on-premise (WAR vs JAR); recommends standalone JAR deployment.
- [[route-finder-datahub-sync]] — design for synchronizing Route Finder database changes to ShipIt DataHub via Kafka CDC; Avro + Debezium preferred; strict additive-only schema evolution required.

## Concepts

### GLS Infrastructure & Security

- [[GlsPki]] — GLS Group Public Key Infrastructure at pki.gls-group.net; issues internal certificates from GLS root CA.
- [[MutualTls]] — mutual TLS authentication pattern; used to secure Apigee API proxies; requires GLS PKI client certificates.
- [[ApigeeKvm]] — Apigee Key Value Maps; encrypted runtime storage for credentials and keys used by API proxies.

### CrossBorder+ Smart Routing — Security

- [[VersionedKeyRotation]] — encryption key versioning approach; distributes partner API encryption key via Apigee proxy; medium maintenance overhead.
- [[TokenBroker]] — preferred approach; Apigee reverse proxy exchanges partner credentials for short-lived tokens; zero maintenance.
- [[ShipItOAuth2]] — ShipIt's existing OAuth2 service reused to authenticate Route Finder against Apigee proxies; preferred authentication approach.

### CrossBorder+ Smart Routing — System Components

- [[ShipIt]] — GLS shipping system with WildFly backend, Synchronizer, and DataHub; being extended for Route Finder integration.
- [[RouteFinder]] — Smart Routing (RTG) component providing routing decisions via REST API; being integrated into on-premise ShipIt.
- [[WildFlyWarDeployment]] — WAR deployment model for Route Finder inside ShipIt's WildFly; blocked by WildFly version conflict.
- [[StandaloneJarDeployment]] — recommended JAR deployment model for Route Finder as independent Windows service.
- [[DataHub]] — ShipIt central data distribution hub; intermediate store between RTG AWS database and customer on-premise databases.
- [[ShipItSynchronizer]] — Spring Boot app per ShipIt instance; consumes DataHub data and pushes to customer backend database.

### CrossBorder+ Smart Routing — Data Synchronization

- [[KafkaCdc]] — Kafka Change Data Capture pipeline (Debezium → Kafka → JDBC sink → DataHub); preferred data sync approach.
- [[DebeziumConnector]] — CDC source connector that reads RTG PostgreSQL WAL and publishes changes to Kafka topics.
- [[Liquibase]] — Route Finder database migration framework; must produce versioned SQL files compatible with ShipIt Component Updater.

## Syntheses

- [[key-distribution-approach-comparison]] - side-by-side comparison of Versioned Key Rotation vs Token Broker; covers DB impact, maintenance, and compromise response; recommends Token Broker + ShipIt OAuth2.

