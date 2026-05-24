---
title: "AWS ECR (Elastic Container Registry)"
type: concept
tags: [aws, devops, container-registry, docker, ecr]
sources:
  - "[raw/devops/Github Action - Java Gradle  Docker build and push - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166043631/Github+Action+-+Java+Gradle+Docker+build+and+push)"
  - "[raw/devops/Github Actions - Onboarding Applications Guide - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042466/Github+Actions+-+Onboarding+Applications+Guide)"
last_updated: 2026-05-24
---

## Definition

AWS ECR is the container image registry used by GLS XB+ for Docker images produced by CI pipelines. XB+ operates three logical AWS accounts (COMMON, KAFKA, DEVOPS), each with its own ECR registry. CI workflows determine the target account from the calling branch and the `aws-account-name` input, then authenticate via the `aws-actions/amazon-ecr-login` action.

## Key Information

**Account/registry mapping in CI:**
- COMMON dev: 217685855605.dkr.ecr.eu-central-1.amazonaws.com
- COMMON prod: 867289402855.dkr.ecr.eu-central-1.amazonaws.com
- Other COMMON/KAFKA/DEVOPS registries: addressed via account-specific secrets.

**Authentication:**
- COMMON dev/stage -> OIDC (no long-lived secrets).
- COMMON prod, KAFKA prod/stage, DEVOPS -> long-lived access keys stored as org secrets (`XBP_COMMON_PROD_AWS_*`, `XBP_KAFKA_PROD_AWS_*`, `XBP_KAFKA_STAGE_AWS_*`, `XBP_DEVOPS_AWS_*`).
- OIDC requires `permissions: id-token: write` in the workflow.

**Branch -> account secret mapping in [[github-actions-gradle-docker-push]]:**
- COMMON + dev -> XBP_COMMON_DEV_*
- COMMON + release/* -> XBP_COMMON_STAGE_*
- COMMON + master|main -> XBP_COMMON_PROD_*
- KAFKA + dev -> XBP_KAFKA_STAGE_*
- KAFKA + master|main -> XBP_KAFKA_PROD_*
- DEVOPS + * -> XBP_DEVOPS_PROD_*

**Build options:**
- Classic: `docker build` + `docker tag` + `docker push`.
- [[Jib]] (Gradle): `./gradlew jib` directly publishes without a Docker daemon.

## Related Links

- [[Jib]] - alternative build option for Java services
- [[GithubActions]] - CI platform pushing to ECR
- [[XbpReusableCi]] - reusable workflow pushing to ECR
- [[XbpDevopsTools]] - host of the gradle-docker-build-push workflow
- [[github-actions-gradle-docker-push]] - Gradle/Jib push workflow
- [[github-actions-onboarding-guide]] - team onboarding guide
- [[AmazonEKS]] - typical deployment target for ECR images
