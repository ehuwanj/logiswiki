---
title: "GitHub Actions Build Workflow - Jenkins Migration Concept"
type: source
tags: [devops, github-actions, jenkins-migration, ci-cd, isrs-15737]
sources:
  - "[raw/devops/Github Actions Build Workflow - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166089345/Github+Actions+Build+Workflow)"
last_updated: 2026-05-24
---

## Summary

Concept document (ISRS-15737) for replacing on-prem Jenkins pipelines with GitHub Actions across XB+. Captures the audit of 426 Jenkins pipelines (only 6% migrate cleanly; 36% partial; 49% unsupported; 7% failed) and identifies the migration challenges: 904 `script` steps are unsupported, custom shared-library steps (`buildsources_gradle`, `sonarqubeanalysis_gradle`, `cleanworkspace`, `withAWS`) are unknown to the importer, and on-prem SonarQube/Sonatype Nexus are unreachable from cloud GitHub Actions runners. Proposes a 6-step migration strategy and a reusable CI/CD workflow template covering build, unit test, integration test, code analysis, tag generation, Docker build/push to ECR, and Nexus publish - with branch-aware gating.

## Key Claims

- Jenkins audit (Feb 2025): 426 pipelines, 27 successful (6%), 154 partial (36%), 211 unsupported (49%), 34 failed (7%).
- Top known build steps: sh (212), echo (159), withCredentials (38), checkout (17), cucumber (13).
- Top unknown steps (from shared library): cleanworkspace (138), withAWS (61), buildsources_gradle (55), sonarqubeanalysis_gradle (40).
- Top unsupported: script (904 occurrences) - over 50% of all build steps.
- `cleanworkspace` is unnecessary in GitHub Actions because each job runs in a fresh environment.
- `withAWS` maps directly to the official `aws-actions/configure-aws-credentials` action.
- On-prem SonarQube and on-prem Sonatype Nexus cannot be reached from GitHub-hosted runners; self-hosted runners are required.
- Six main Jenkins pipeline categories: Apigee proxy deploy, Docker image build, service deploy, infra (Terragrunt/Terraform) deploy, frontend deploy, smoketests.
- Standard pipeline stages: Prepare Build -> Build Application -> Unit Test -> Integration Test -> Code Analysis -> Tag Generation -> Build and Push Docker -> Publish Artifact -> Trigger Deployment.
- Recommended: convert Apigee global Jenkins pipeline to a reusable workflow template; convert helpers like `getNotificationUrl` to custom composite actions.

## Evidence and Notes

- Migration strategy: (1) evaluate pipelines with GitHub Actions Importer, (2) define reusable workflows with `workflow_call`, (3) migrate shared library functions to custom actions in a private GHE repo, (4) develop and test incrementally, (5) combine reusable workflows + custom actions, (6) document and train.
- Reusable workflow template uses inputs: `java-version`, `container-registry` (defaulted via branch: dev = 217685855605.dkr.ecr.eu-central-1.amazonaws.com else 867289402855), `run-integration-test`, `run-code-analysis`, `run-tag-generation`, `run-docker-build`, `run-publish-artifact`.
- Branch-aware defaults: tag generation, docker build, and Nexus publish default to `true` only on main/master/dev.
- Tag pattern: `v1.0.${{ github.run_number }}`.
- Jib commonly used for Docker builds, but not in all services.
- Some heritage triggers (BitBucketTrigger, GenericTrigger) are unsupported by the importer.

## Related Links

- [[GithubActions]] - target platform
- [[ReusableWorkflow]] - core abstraction
- [[XbpReusableCi]] - the implemented reusable CI workflow
- [[XbpDevopsTools]] - shared workflow repository
- [[SonarQube]] - on-prem code analysis
- [[SonatypeNexus]] - on-prem artifact repository
- [[AwsEcr]] - Docker image destination
- [[Jib]] - Java Docker image builder
- [[github-actions-blueprint-workflow]] - architectural blueprint
- [[github-actions-onboarding-guide]] - team-facing onboarding
- [[github-actions-workflow-dynamic-elements]] - dynamic input parameter design
- [[github-actions-reusable-workflow-versioning]] - versioning strategy
- [[github-actions-gradle-docker-push]] - Gradle/Jib Docker push reusable workflow
