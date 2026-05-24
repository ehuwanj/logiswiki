---
title: "GitHub Actions - Java Gradle Docker Build and Push Reusable Workflow"
type: source
tags: [devops, github-actions, docker, gradle, jib, aws-ecr]
sources:
  - "[raw/devops/Github Action - Java Gradle  Docker build and push - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166043631/Github+Action+-+Java+Gradle+Docker+build+and+push)"
last_updated: 2026-05-24
---

## Summary

Documentation for the `gradle-docker-build-push.yaml` reusable workflow in `gls-group/xbp-devops-tools`. Triggers via `workflow_dispatch` and builds a Java/Gradle project's Docker image, pushing it to one of three AWS accounts (COMMON, KAFKA, DEVOPS) based on the calling branch. Supports either classic `docker build/push` or Gradle Jib. Branch determines which AWS access key pair the workflow consumes: dev/release/master each map to dev/stage/prod credentials within the chosen account; DEVOPS uses a single key pair for any branch.

## Key Claims

- Reusable workflow URL: `gls-group/xbp-devops-tools/.github/workflows/gradle-docker-build-push.yaml@dev`.
- Required inputs: `source_repo`, `ecr_repository`, `branch_name`, `aws_account_name` (COMMON | KAFKA | DEVOPS), `useJib` (boolean), `image_tag`.
- Branch-to-AWS-account mapping:
  - COMMON + dev -> XBP_COMMON_DEV_*
  - COMMON + release/* -> XBP_COMMON_STAGE_*
  - COMMON + master|main -> XBP_COMMON_PROD_*
  - KAFKA + dev -> XBP_KAFKA_STAGE_*
  - KAFKA + master|main -> XBP_KAFKA_PROD_*
  - DEVOPS + * -> XBP_DEVOPS_PROD_*
- Mismatched branch/account combinations resolve to `unknown` and abort the workflow.
- When `useJib: true`, the workflow uses Gradle Jib to build and push; otherwise it uses classic docker build/tag/push.
- All AWS secret pairs should be passed to be safe; minimal sets are acceptable when targeting a specific account.

## Evidence and Notes

- Workflow steps: Checkout code -> Identify branch and set AWS_ACCOUNT_PREFIX -> Fail-if-unknown guard -> Configure AWS Credentials -> Login to ECR -> Build/push (Jib or classic).
- Sample image_tag pattern: `1.1.0-docker-jib`.
- Example consumer at `gls-group/gradle-poc/blob/dev/.github/workflows/docker.yaml`.

## Related Links

- [[XbpDevopsTools]] - host repository
- [[ReusableWorkflow]] - reusable workflow pattern
- [[GithubActions]] - underlying technology
- [[AwsEcr]] - destination registries
- [[Jib]] - Gradle Jib build option
- [[github-actions-build-workflow-concept]] - originating concept
- [[github-actions-onboarding-guide]] - related onboarding guide
