---
title: "GitHub Actions"
type: concept
tags: [devops, github-actions, ci-cd]
sources:
  - "[raw/devops/Github Actions - Blueprint Workflow Definition - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042083/Github+Actions+-+Blueprint+Workflow+Definition)"
  - "[raw/devops/Github Actions Build Workflow - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166089345/Github+Actions+Build+Workflow)"
  - "[raw/devops/Github Actions - Onboarding Applications Guide - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042466/Github+Actions+-+Onboarding+Applications+Guide)"
last_updated: 2026-05-24
---

## Definition

GitHub Actions is the CI/CD automation platform built into GitHub Enterprise. Within GLS XB+ it is the replacement target for on-prem Jenkins pipelines (ISRS-15737). Workflows are YAML files under `.github/workflows/<file>.yml` that respond to triggers (`push`, `pull_request`, `workflow_dispatch`, `schedule`, `workflow_call`) and run jobs on either GitHub-hosted runners or self-hosted runners that can reach on-prem services like Sonatype Nexus and SonarQube.

## Key Information

**Core building blocks:**
- **Workflow**: YAML file under `.github/workflows/`. Contains `name`, `on` (triggers), `jobs`, and optional permissions/env blocks.
- **Job**: Group of steps running on one runner. Identified by job-id (`ci:`, `build:`, etc.).
- **Step**: A single command (`run:`) or action invocation (`uses:`).
- **Action**: Reusable unit. Can be: (1) marketplace/standard action (`uses: actions/checkout@v4`), (2) custom composite action ([[CompositeAction]]), or (3) Docker-based action.
- **Reusable Workflow**: A workflow declared with `on: workflow_call`, callable from other workflows ([[ReusableWorkflow]]).

**Triggers:**
- `push`, `pull_request` - filterable by `branches:` (wildcards `release/**`, negations `!feature/**`).
- `workflow_dispatch` - manual trigger from UI.
- `schedule` - cron.
- `workflow_call` - invoked by other workflows.

**Context and expressions:**
- `${{ }}` syntax for dynamic values.
- Common contexts: `github.ref`, `github.ref_name`, `github.event_name`, `github.repository`, `github.event.repository.name`, `github.run_number`.
- Secrets accessed as `${{ secrets.NAME }}`; passed through to reusable workflows via the `secrets:` block.

**Permissions and OIDC:**
- `permissions: id-token: write, contents: read` required for OIDC authentication against AWS.
- COMMON dev/stage AWS accounts use OIDC; COMMON prod, KAFKA prod/stage, and DEVOPS use long-lived access keys stored as org secrets (see [[github-actions-onboarding-guide]]).

**Branch behavior in GLS XB+ reusable CI:**
- master/dev/release/* -> full pipeline (build + test + SonarQube + Docker push + Nexus publish).
- PRs to master -> build + test + SonarQube only (no Docker push, no Nexus publish).

**Migration from Jenkins (ISRS-15737):**
- Audit (Feb 2025) found 6% of 426 Jenkins pipelines migrated cleanly; over 50% of all build steps were `script` (unsupported).
- Custom shared-library steps (cleanworkspace, withAWS, buildsources_gradle, sonarqubeanalysis_gradle) are unknown to the importer and must be re-implemented as actions or workflows.
- On-prem SonarQube and Sonatype Nexus require self-hosted runners (`use-internal-nexus: true`).

## Related Links

- [[ReusableWorkflow]] - core reuse abstraction
- [[CompositeAction]] - encapsulated multi-step action
- [[XbpDevopsTools]] - shared workflow repository
- [[XbpReusableCi]] - the common reusable CI workflow
- [[SonarQube]] - code analysis target
- [[SonatypeNexus]] - artifact repository
- [[AwsEcr]] - container registry
- [[Jib]] - Java Docker image builder option
- [[SemanticReleaseTool]] - automated versioning tool for shared workflows
- [[github-actions-blueprint-workflow]] - architectural blueprint source
- [[github-actions-build-workflow-concept]] - Jenkins migration concept
- [[github-actions-onboarding-guide]] - team onboarding guide
- [[github-actions-workflow-dynamic-elements]] - dynamic input design
- [[github-actions-gradle-docker-push]] - Gradle Docker reusable workflow
- [[github-actions-reusable-workflow-versioning]] - versioning strategy
