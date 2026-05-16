---
title: "WildFly WAR Deployment"
type: concept
tags: [wildfly, deployment, war, shipit, route-finder, java, jakarta-ee]
sources:
  - "[raw/routing/ShipIt Integration Route Finder Deployment Comparison - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166064255/ShipIt+Integration+Route+Finder+Deployment+Comparison)"
  - "[raw/routing/WildFly-ShipIT Integration Analysis - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166063571/WildFly-ShipIT+Integration+Analysis)"
last_updated: 2026-05-16
---

## Definition

WAR (Web Application Archive) deployment on WildFly is an option for running Route Finder inside the ShipIt backend's existing WildFly servlet container, rather than as a separate process. The WAR file (e.g., `rtg.war`) is copied to the WildFly deployment folder and runs under the same JVM as the ShipIt backend. This requires WildFly 38 (Jakarta EE 10), which ShipIt currently does not use but has planned for ShipIt 6.x.

## Key Information

**How it works:**
- ShipIt installer unpacks `rtg.war` to WildFly deployment folder; environment variables injected via `standalone.conf.bat`; Route Finder starts automatically with ShipIt backend.
- URL: `https://127.0.0.1:8443/rtg/routefinder/v1/` (or `ROOT.war` for root context)

**Building the WAR:**
- Build command: `./gradlew bootWar` (produces `build/libs/route-finder-X.Y.Z.war`)
- Required: `RouteFinderServletInitializer` class extending `SpringBootServletInitializer` (provides WildFly entry point)
- Required: `jboss-deployment-structure.xml` in `WEB-INF/` to exclude WildFly's built-in Jakarta REST provider (RESTEasy) and force the app's own Jersey/CXF provider
- WildFly 38 deployment: copy WAR to `standalone/deployments/`; verify `.deployed` marker appears

**WildFly version compatibility:**
- Route Finder requires Jakarta EE 10 (Spring Boot 3.x, `jakarta.*` namespace)
- WildFly 26: Jakarta EE 9 (`jakarta.*` partially) - incompatible
- WildFly 38: Jakarta EE 10 - compatible with Route Finder
- ShipIT upgrade from WildFly 26 to WildFly 38 planned for ShipIt 6.x (before October 2026)

**Pros:**
- No additional process or Windows service configuration
- Smaller memory footprint (~50-150 MB saved vs standalone JVM)
- WildFly manages lifecycle

**Cons:**
- Route Finder dependency upgrades constrained by ShipIt's WildFly version
- Shared JVM: GC tuning and heap sizing must be re-tested for combined workload
- Shared JVM: OOM root-cause analysis is more difficult

**Configuration:** Environment variables in `standalone.conf.bat` (e.g., `set "JAVA_OPTS=%JAVA_OPTS% -DDB_PASSWORD=*****"`).

**Status:** Requires WildFly 38 upgrade (planned ShipIt 6.x). Until then, standalone JAR is recommended.

## Related Links

- [[StandaloneJarDeployment]] - recommended alternative deployment model
- [[RouteFinder]] - component being deployed
- [[ShipIt]] - host system providing the WildFly container
- [[shipit-route-finder-deployment-comparison]] - source document with full comparison
- [[route-finder-wildfly-integration]] - WildFly integration analysis with build steps
