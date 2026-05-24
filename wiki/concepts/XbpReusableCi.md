---
title: "XB+ Reusable CI Workflow"
type: concept
tags: [devops, github-actions, reusable-workflow, ci-cd]
sources:
  - "[raw/devops/Github Actions - Onboarding Applications Guide - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042466/Github+Actions+-+Onboarding+Applications+Guide)"
  - "[raw/devops/Github Actions Workflow Dynamic Elements - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166090734/GitHub+Actions+Workflow+Dynamic+Elements)"
  - "[raw/devops/Github Actions Build Workflow - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166089345/Github+Actions+Build+Workflow)"
last_updated: 2026-05-24
---

## Definition

`common.reusable-ci.yaml` is the primary reusable GitHub Actions workflow in [[XbpDevopsTools]] that provides a standardized CI/CD pipeline for all XB+ applications. Consumers reference it via `uses: gls-group/xbp-devops-tools/.github/workflows/common.reusable-ci.yaml@master`. The workflow supports four tech stacks (java, node, python, go) via the `tech-stack` input and handles build, unit tests, integration tests, SonarQube analysis, optional Docker push to ECR, optional Nexus publish, and branch-aware tag generation.

## Key Information

**Required inputs:**
- `tech-stack`: java | node | python | go.
- `version`: tech-stack version string (e.g., '17', '20', '3.11', '1.21').

**Common optional inputs:**
- `working-directory` (default `.`).
- `docker-image-name` (default `my-app`).
- `aws-account-name`: COMMON | KAFKA | DEVOPS (default COMMON).
- `run-integration-test`, `run-code-analysis` (defaults true).
- `run-tag-generation`, `run-docker-build`, `run-publish-artifact` (default true only on master/dev).
- `use-internal-nexus` (default true on master/dev) - routes to self-hosted runners that can reach on-prem Nexus.
- `fail-on-quality-gate` (default false).
- `sonar-project-key` (default repo name).
- `build-command`, `test-command`, `integration-test-command` - escape hatches for custom commands.
- `custom-env-vars` (comma-separated).
- `runner` (default `ubuntu-latest`).

**Required secret:**
- `SONAR_TOKEN` - SonarQube authentication.

**Optional secrets (feature-gated):**
- Nexus: `NEXUS_USERNAME`, `NEXUS_PASSWORD`, `NEXUS_CA_CERT` (base64-encoded CA).
- AWS ECR (long-lived): `XBP_COMMON_PROD_AWS_*`, `XBP_KAFKA_PROD_AWS_*`, `XBP_KAFKA_STAGE_AWS_*`, `XBP_DEVOPS_AWS_*`.
- COMMON dev/stage use OIDC (no AWS secrets needed).

**Required workflow permissions:**
- `id-token: write` (OIDC).
- `contents: read`.

**Branch behavior:**
- master / dev / release/* -> Build, Test, SonarQube, Docker push, Nexus publish.
- PR to master -> Build, Test, SonarQube only.
- Other branches -> Build, Test, SonarQube only.

**Pipeline stages (from migration concept):**
1. Prepare build (env vars).
2. Build application (Maven/Gradle/npm/pip/go).
3. Unit tests.
4. Integration tests.
5. Code analysis (SonarQube).
6. Tag generation (`v1.0.${{ github.run_number }}`).
7. Build and push Docker (classic or via [[Jib]]).
8. Publish artifact to [[SonatypeNexus]].
9. Trigger deployment (TBD - may be replaced by ArgoCD).

**Java auto-detection:**
- pom.xml -> Maven (`mvn clean install` / `mvn test` / `mvn verify`).
- build.gradle / build.gradle.kts -> Gradle (`./gradlew build` / `./gradlew test` / `./gradlew integrationTest`).

## Related Links

- [[XbpDevopsTools]] - hosting repository
- [[ReusableWorkflow]] - underlying abstraction
- [[GithubActions]] - host platform
- [[SonarQube]] - code analysis target
- [[SonatypeNexus]] - artifact repository
- [[AwsEcr]] - Docker push destination
- [[Jib]] - Java Docker image builder option
- [[github-actions-onboarding-guide]] - end-user guide
- [[github-actions-workflow-dynamic-elements]] - dynamic input design
- [[github-actions-build-workflow-concept]] - originating Jenkins migration concept
- [[github-actions-reusable-workflow-versioning]] - versioning strategy
