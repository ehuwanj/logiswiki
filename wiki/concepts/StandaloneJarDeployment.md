---
title: "Standalone JAR Deployment"
type: concept
tags: [shipit, deployment, jar, route-finder, java, windows-service]
sources: ["[raw/routing/ShipIt Integration Route Finder Deployment Comparison - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166064255/ShipIt+Integration+Route+Finder+Deployment+Comparison)"]
last_updated: 2026-05-10
---

## Definition

Standalone JAR deployment is the **recommended** model for running Route Finder together with ShipIt on-premise. Route Finder runs as an independent Java process with its own JVM, started as a separate Windows service via a provided startup script. This provides full isolation from ShipIt's WildFly container and allows independent dependency management.

## Key Information

- **Install path**: `gls\shipit_backend\rtg\`
- **URL**: `https://127.0.0.1:8444/routefinder/v1/`
- **Startup**: `startRouteFinder.bat` - sets all `JAVA_OPTS` environment variables and runs `java %JAVA_OPTS% -jar route-finder-3.jar`. Registered as a Windows service.
- **HTTPS**: A new keystore must be created for Route Finder HTTPS; ShipIt backend must have a corresponding trust store to connect to it securely.
- **Logging**: `C:\gls\shipit_backend\log\rtg\route-finder.log`
- **Pros**:
  - Independent dependency upgrades - Route Finder WildFly/Spring version not constrained by ShipIt
  - Strong isolation: crashes in Route Finder do not affect ShipIt JVM
  - Easier troubleshooting - separate process and log file
- **Cons**:
  - Additional Windows service: if the PC/laptop is restarted, Route Finder must also restart - additional operational risk
  - New HTTPS port required: firewall rules may need updating
  - New keystore configuration required
- **Port configuration**: New port displayed in ShipIt installer GUI; configurable during installation.
- **Why recommended**: WildFly version conflict (WAR option blocked) and better long-term independence justify the extra operational complexity.

## Related Links

- [[WildFlyWarDeployment]] - alternative deployment model (not recommended due to WildFly version conflict)
- [[RouteFinder]] - component deployed as standalone JAR
- [[ShipIt]] - host system the JAR is deployed alongside
- [[shipit-route-finder-deployment-comparison]] - source document with full comparison and startup script example
