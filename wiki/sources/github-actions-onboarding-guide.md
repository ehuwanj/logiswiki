---
title: "GitHub Actions - Onboarding Applications Guide"
type: source
tags: [devops, github-actions, ci-cd, onboarding]
sources:
  - "[raw/devops/Github Actions - Onboarding Applications Guide - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042466/Github+Actions+-+Onboarding+Applications+Guide)"
last_updated: 2026-05-24
---

## Summary

Team-facing onboarding guide for the GLS XB+ Reusable CI Pipeline hosted in the `gls-group/xbp-devops-tools` repository. Teams add a single `.github/workflows/ci.yaml` that calls the reusable workflow `common.reusable-ci.yaml@master`, supplying `tech-stack` (java/node/python/go), `version`, and optional inputs (docker image name, AWS account, runners, custom commands). Organization-level secrets (SONAR_TOKEN, NEXUS_*, XBP_*_AWS_*) are managed by team #Navy and require explicit whitelisting per repository. Workflow behavior differs by branch: master/dev/release/* run the full build+test+push pipeline, while PRs run build+test only.

## Key Claims

- Reusable workflow path: `gls-group/xbp-devops-tools/.github/workflows/common.reusable-ci.yaml@master`.
- Required inputs: `tech-stack` and `version` (string-quoted to avoid YAML number interpretation).
- Required secret: `SONAR_TOKEN` for SonarQube analysis.
- Nexus publishing requires NEXUS_USERNAME, NEXUS_PASSWORD, NEXUS_CA_CERT (base64-encoded CA cert).
- AWS ECR pushes use long-lived access keys for COMMON prod, KAFKA prod/stage, DEVOPS accounts; COMMON dev/stage use OIDC (no AWS secrets needed).
- Workflows require `permissions: id-token: write` and `contents: read` for OIDC authentication with AWS.
- Branch behavior: master/dev/release/* push Docker and publish Nexus; PRs build+test only.
- Repo must be whitelisted at the organization level by team #Navy to access secrets.

## Evidence and Notes

- Tech-stack-specific build files required: pom.xml (Maven), build.gradle (Gradle), package.json (Node), requirements.txt or setup.py (Python), go.mod (Go).
- Optional inputs include working-directory, docker-image-name, aws-account-name (COMMON/KAFKA/DEVOPS), run-integration-test, run-code-analysis, run-tag-generation, run-docker-build, run-publish-artifact, use-internal-nexus, fail-on-quality-gate, sonar-project-key, build-command/test-command/integration-test-command (custom), custom-env-vars, runner.
- `use-internal-nexus` is required to use self-hosted runners that can reach on-prem Nexus.
- PR-only build pattern: use `${{ github.event_name != 'pull_request' }}` for run-docker-build, run-publish-artifact, run-tag-generation.
- Monorepo pattern: define multiple jobs (backend, frontend) each calling the reusable workflow with different working-directory and tech-stack values.
- Support contact: team #Navy via Heat ticket or Jira; documentation hub at "Common Reusable CI" page.

## Related Links

- [[XbpReusableCi]] - the reusable CI workflow this guide describes
- [[XbpDevopsTools]] - the shared workflow repository
- [[GithubActions]] - underlying technology
- [[ReusableWorkflow]] - reusable workflow pattern
- [[SonarQube]] - code analysis target
- [[SonatypeNexus]] - artifact repository
- [[AwsEcr]] - container registry destinations
- [[github-actions-build-workflow-concept]] - original migration concept
- [[github-actions-blueprint-workflow]] - architectural blueprint
- [[github-actions-workflow-dynamic-elements]] - parameter design
