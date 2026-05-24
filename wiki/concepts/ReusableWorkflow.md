---
title: "Reusable Workflow"
type: concept
tags: [devops, github-actions, reusable-workflow, ci-cd]
sources:
  - "[raw/devops/Github Actions - Blueprint Workflow Definition - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042083/Github+Actions+-+Blueprint+Workflow+Definition)"
  - "[raw/devops/Github Actions Workflow Dynamic Elements - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166090734/GitHub+Actions+Workflow+Dynamic+Elements)"
  - "[raw/devops/Github Actions Reusable Workflow Versioning - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166035845/Github+Actions+Reusable+Workflow+Versioning)"
last_updated: 2026-05-24
---

## Definition

A Reusable Workflow is a GitHub Actions workflow declared with `on: workflow_call`, allowing other workflows to invoke it as a unit via `uses: owner/repo/.github/workflows/<file>.yml@<ref>`. Inputs are declared under `on.workflow_call.inputs` with types (string/boolean/number) and required/default; secrets are declared under `on.workflow_call.secrets` and passed explicitly. Reusable workflows centralize CI/CD pipelines across many repositories, supporting parameterization, branch-aware defaults, and consistent quality gates.

## Key Information

**Definition syntax:**
```yaml
on:
  workflow_call:
    inputs:
      tech-stack: { required: true, type: string }
      version: { required: true, type: string }
    secrets:
      SONAR_TOKEN: { required: true }
```

**Call syntax:**
```yaml
jobs:
  ci:
    uses: gls-group/xbp-devops-tools/.github/workflows/common.reusable-ci.yaml@master
    with:
      tech-stack: java
      version: '17'
    secrets:
      SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

**XB+ common reusable CI design ([[github-actions-workflow-dynamic-elements]]):**
- Single workflow handles java/node/python/go via `tech-stack` input.
- Java auto-detects Maven (pom.xml) vs Gradle (build.gradle).
- Branch-aware defaults: tag-generation, docker-build, publish-artifact default true only on main/master/dev.
- Optional command overrides: build-command, test-command, integration-test-command, code-analysis-command.

**Versioning ([[github-actions-reusable-workflow-versioning]]):**
- SemVer 2.0.0 + floating major tag (e.g., `v1` points to latest `v1.*.*`).
- Tagging via `semantic-release` + `semantic-release-major-tag`.
- Consumers pin a fixed tag (`@v1.0.0`) for reproducibility, or use the floating tag (`@v1`) for auto-updates.
- No LTS; in-flight features available via `@dev` branch reference.

**Use cases in GLS XB+:**
- `common.reusable-ci.yaml` - the primary CI workflow ([[XbpReusableCi]]).
- `gradle-docker-build-push.yaml` - Gradle/Jib Docker push ([[github-actions-gradle-docker-push]]).
- Apigee global pipeline conversion - in progress per migration plan.

## Related Links

- [[GithubActions]] - host platform
- [[CompositeAction]] - alternative reuse abstraction for step bundles
- [[XbpReusableCi]] - the primary reusable CI workflow at GLS
- [[XbpDevopsTools]] - hosting repository
- [[SemanticReleaseTool]] - automation behind floating major tags
- [[SemanticVersioning]] - underlying versioning scheme
- [[github-actions-blueprint-workflow]] - architectural source
- [[github-actions-workflow-dynamic-elements]] - dynamic input design
- [[github-actions-reusable-workflow-versioning]] - versioning policy
- [[github-actions-gradle-docker-push]] - Gradle Docker example
- [[github-actions-build-workflow-concept]] - Jenkins migration concept
