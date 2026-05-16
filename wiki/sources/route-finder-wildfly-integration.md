---
title: "ShipIt Integration: Making Route Finder ready for WildFly"
type: source
tags: [source, routing, wildfly, deployment, route-finder]
sources:
  - "[raw/routing/ShipIt Integration Making Route Finder ready for WildFly.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166063292/ShipIt+Integration+Making+Route+Finder+ready+for+WildFly)"
last_updated: 2026-05-16
---

## Summary

Route Finder runs as a Spring Boot 3.5.7 JAR with embedded Tomcat by default, but ShipIT hosts applications on JBoss WildFly. The primary incompatibility is a Jakarta EE version mismatch: Route Finder uses Jakarta EE 10 (Hibernate 6.x, JPA 3.1) while WildFly 26 only supports Jakarta EE 9 (Hibernate 5.x, JPA 3.0). The recommended resolution is to upgrade to WildFly 38 which supports Jakarta EE 10 and Spring Boot 3.x without requiring any Route Finder code changes. Implementation requires adding WAR packaging support to build.gradle, a SpringBootServletInitializer class, and a jboss-deployment-structure.xml file.

## Key Claims

- WildFly 26 is incompatible with Route Finder due to Jakarta EE 9 vs EE 10 mismatch; specifically GenerationType.UUID (JPA 3.1) does not exist in JPA 3.0.
- Upgrade to WildFly 38 is the recommended path; WildFly 26 is no longer actively maintained.
- Route Finder builds as JAR by default; WAR build is triggered only via `./gradlew bootWar` (conditional Gradle block using `isWarTask`).
- Memory footprint: Route Finder ~900 MB RAM at startup (due to caching), image size ~164 MB.
- Three solution options considered: (1) upgrade WildFly (recommended - no code changes), (2) downgrade Spring Boot 2.7.x (significant code changes), (3) exclude all Jakarta/Hibernate deps (complex).

## Evidence and Notes

- Route Finder: Spring Boot 3.5.7, Java 17, Gradle, JAR, PostgreSQL 14.17, Hibernate 6.x.
- ShipIT: Java 17, WAR, WildFly 26 Preview (Jakarta EE 9).
- WildFly 38 deployment: WAR goes into `wildfly-38.0.1.Final\standalone\deployments`; start with `.\standalone.bat`.
- jboss-deployment-structure.xml declares dependency on `jdk/net` system module to prevent ClassNotFoundException.
- Environment variables must be set in `standalone.conf.bat` (DB credentials, partner API secrets, etc.).

## Related Links

- [[RouteFinder]] - the Route Finder service
- [[WildFlyWarDeployment]] - WAR deployment concept
- [[ShipIt]] - ShipIT system hosting WildFly
- [[shipit-route-finder-deployment-comparison]] - earlier deployment comparison (JAR vs WAR)
