---
title: "Sonatype Nexus"
type: concept
tags: [devops, artifact-repository, nexus, on-prem]
sources:
  - "[raw/devops/Github Actions Build Workflow - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166089345/Github+Actions+Build+Workflow)"
  - "[raw/devops/Github Actions - Onboarding Applications Guide - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042466/Github+Actions+-+Onboarding+Applications+Guide)"
last_updated: 2026-05-24
---

## Definition

Sonatype Nexus is the on-premise artifact repository used by GLS XB+ to store Java artifacts (jars, etc.) produced by builds. It runs on-prem and is not reachable from GitHub-hosted runners, so workflows that publish to Nexus must execute on self-hosted runners and supply credentials plus a base64-encoded CA certificate.

## Key Information

- Required secrets when `run-publish-artifact: true`:
  - `NEXUS_USERNAME`
  - `NEXUS_PASSWORD`
  - `NEXUS_CA_CERT` (base64-encoded CA certificate)
- `use-internal-nexus: true` workflow input routes the job to self-hosted runners that can reach the on-prem Nexus.
- `maven-releases` repository within Nexus is configured immutable - re-publishing the same coordinates is rejected (see [[SemanticVersioning]] for the versioning rules built around this).
- Migration consideration: SonarQube and Nexus together drive the requirement for self-hosted runners in any production-grade XB+ pipeline.

## Related Links

- [[GithubActions]] - host platform
- [[XbpReusableCi]] - reusable workflow that publishes to Nexus
- [[SonarQube]] - similarly on-prem service requiring self-hosted runners
- [[SemanticVersioning]] - Nexus `maven-releases` immutability shapes versioning rules
- [[github-actions-build-workflow-concept]] - Jenkins-to-Actions migration concept
- [[github-actions-onboarding-guide]] - onboarding guide that documents NEXUS_* secrets
