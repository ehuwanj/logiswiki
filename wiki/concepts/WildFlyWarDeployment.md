---
title: "WildFly WAR Deployment"
type: concept
tags: [wildfly, deployment, war, shipit, route-finder, java]
sources: ["[raw/routing/ShipIt Integration Route Finder Deployment Comparison - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166064255/ShipIt+Integration+Route+Finder+Deployment+Comparison)"]
last_updated: 2026-05-10
---

## Definition

WAR (Web Application Archive) deployment on WildFly is an option for running Route Finder inside the ShipIt backend's existing WildFly servlet container, rather than as a separate process. The WAR file (e.g., `rtg.war`) is copied to the WildFly deployment folder and runs under the same JVM as the ShipIt backend.

## Key Information

- **How it works**: ShipIt installer unpacks `rtg.war` to WildFly deployment folder; environment variables injected via `standalone.conf.bat`; Route Finder starts automatically with ShipIt backend.
- **URL**: `https://127.0.0.1:8443/rtg/routefinder/v1/` (or `ROOT.war` for root context)
- **Pros**:
  - No additional process or Windows service configuration
  - Smaller memory footprint (~50–150 MB saved vs standalone JVM)
  - WildFly manages lifecycle
- **Cons**:
  - **Critical blocker**: Route Finder requires WildFly 38; ShipIt backend currently supports only WildFly 26. Upgrade to WildFly 38 planned for ShipIt 6.x (before October 2026).
  - Route Finder dependency upgrades constrained by ShipIt's WildFly version
  - Shared JVM: GC tuning and heap sizing must be re-tested for combined workload
  - Shared JVM: OOM root-cause analysis is more difficult
- **Configuration**: Environment variables in `standalone.conf.bat` (e.g., `set "JAVA_OPTS=%JAVA_OPTS% -DDB_PASSWORD=*****"`).
- **Status**: Not currently recommended due to WildFly version conflict.

## Related Links

- [[StandaloneJarDeployment]] — recommended alternative deployment model
- [[RouteFinder]] — component being deployed
- [[ShipIt]] — host system providing the WildFly container
- [[shipit-route-finder-deployment-comparison]] — source document with full comparison
