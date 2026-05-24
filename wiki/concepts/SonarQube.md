---
title: "SonarQube"
type: concept
tags: [devops, code-quality, sonarqube, on-prem]
sources:
  - "[raw/devops/Github Actions Build Workflow - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166089345/Github+Actions+Build+Workflow)"
  - "[raw/devops/Github Actions - Onboarding Applications Guide - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042466/Github+Actions+-+Onboarding+Applications+Guide)"
last_updated: 2026-05-24
---

## Definition

SonarQube is the static code analysis platform used by GLS XB+ for code quality and quality-gate enforcement. It runs on-premise and is therefore unreachable from GitHub-hosted runners; CI workflows that need SonarQube analysis must run on self-hosted runners with network access to the on-prem instance.

## Key Information

- Authentication via `SONAR_TOKEN` org-level secret. The token is the only required secret for the XB+ Reusable CI workflow.
- Per-project key configurable via `sonar-project-key` workflow input (default: repository name).
- Quality gate enforcement controlled by `fail-on-quality-gate` input (default false).
- Internal SonarQube cannot be reached from GitHub-hosted runners; consumers must set `use-internal-nexus: true` (which also routes to self-hosted runners) when on-prem access is required.
- The Jenkins migration audit (ISRS-15737) found 40 occurrences of the custom `sonarqubeanalysis_gradle` step plus 5 `withSonarQubeEnv` and 5 `waitForQualityGate` - all flagged as unknown by the importer.
- Migration option for cloud runners: spin up a SonarQube container in-job (`docker run -d sonarqube:latest`), wait for `/api/system/status` to return UP, run analysis, then teardown. This is a fallback when on-prem cannot be reached.

## Related Links

- [[GithubActions]] - host platform
- [[XbpReusableCi]] - reusable workflow that integrates SonarQube
- [[SonatypeNexus]] - similarly on-prem artifact repository
- [[github-actions-build-workflow-concept]] - Jenkins-to-Actions migration concept
- [[github-actions-onboarding-guide]] - onboarding guide that lists SONAR_TOKEN as required
