---
title: "xbp-devops-tools Repository"
type: concept
tags: [devops, github-actions, repository, shared-workflows]
sources:
  - "[raw/devops/Github Actions - Onboarding Applications Guide - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042466/Github+Actions+-+Onboarding+Applications+Guide)"
  - "[raw/devops/Github Action - Java Gradle  Docker build and push - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166043631/Github+Action+-+Java+Gradle+Docker+build+and+push)"
  - "[raw/devops/Github Actions Reusable Workflow Versioning - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166035845/Github+Actions+Reusable+Workflow+Versioning)"
last_updated: 2026-05-24
---

## Definition

`gls-group/xbp-devops-tools` is the dedicated GitHub Enterprise repository that hosts the GLS XB+ shared GitHub Actions reusable workflows and composite actions. It is owned by the DevOps team (#Navy) and is the single source of truth for CI/CD automation patterns across XB+ applications. Consumer repositories reference workflows from it via `uses: gls-group/xbp-devops-tools/.github/workflows/<file>.yaml@<ref>`.

## Key Information

**Hosted workflows:**
- `common.reusable-ci.yaml` - primary reusable CI ([[XbpReusableCi]]); supports java/node/python/go.
- `gradle-docker-build-push.yaml` - Gradle/Jib Docker build and push to AWS ECR ([[github-actions-gradle-docker-push]]).
- Apigee global pipeline conversion - planned per migration concept.

**Repository structure:**
- `.github/workflows/` - reusable workflow YAMLs.
- `.github/actions/` - composite actions for shared helpers.
- `docs/` - CHANGELOG.md, CONTRIBUTING.md, MIGRATION_GUIDE.md.
- `tests/` - example/test workflow YAMLs to exercise the shared workflows.
- `src/scripts/` - shell scripts called by workflows (setup.sh, deploy.sh).
- `README.md` - overview and usage.

**Versioning ([[github-actions-reusable-workflow-versioning]]):**
- SemVer X.Y.Z tags plus floating major tag (e.g., `v1`).
- Automated by `semantic-release` + `semantic-release-major-tag` plugin.
- No LTS; consumers may pin any fixed tag or use floating major.

**Branch references seen in consumers:**
- `@master` (stable) - most production calls.
- `@dev` (experimental) - opt-in for in-flight features.

**Ownership and governance:**
- Owned by team #Navy (DevOps).
- Org-level secrets accessible only to whitelisted repos; requests go through Heat ticket to #Navy.
- Deprecation policy: 2 sprints notice via XB+ Platform Products Teams channel; entry in CHANGELOG + migration guide.

## Related Links

- [[GithubActions]] - host platform
- [[ReusableWorkflow]] - core artifact
- [[CompositeAction]] - companion artifact for step bundles
- [[XbpReusableCi]] - the common CI workflow hosted here
- [[SemanticReleaseTool]] - automated versioning
- [[github-actions-onboarding-guide]] - team-facing usage guide
- [[github-actions-build-workflow-concept]] - originating Jenkins migration concept
- [[github-actions-reusable-workflow-versioning]] - versioning strategy
- [[github-actions-gradle-docker-push]] - one of the hosted workflows
