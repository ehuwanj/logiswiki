---
title: "Gradle Jib"
type: concept
tags: [devops, docker, gradle, jib, container-image]
sources:
  - "[raw/devops/Github Action - Java Gradle  Docker build and push - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166043631/Github+Action+-+Java+Gradle+Docker+build+and+push)"
  - "[raw/devops/Github Actions Build Workflow - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166089345/Github+Actions+Build+Workflow)"
last_updated: 2026-05-24
---

## Definition

Jib is Google's Gradle/Maven plugin that builds optimized Docker container images for Java applications without requiring a Docker daemon or Dockerfile. It splits the image into layers (dependencies, resources, classes) for faster incremental builds and pushes directly to a target registry. In GLS XB+ CI, Jib is the default Docker builder for Java services and is opt-in via `useJib: true` on the `gradle-docker-build-push.yaml` reusable workflow.

## Key Information

- Activated in the reusable workflow via `useJib: true`. When false, the workflow falls back to classic `docker build` / `docker tag` / `docker push`.
- Requires only Gradle on the runner; no Docker daemon needed.
- Pushes directly to AWS ECR using the configured registry credentials.
- Image tag pattern in XB+ CI: `<version>-docker-jib` (e.g., `1.1.0-docker-jib`).
- Used in many existing Jenkins pipelines per the migration audit, alongside classic docker builds (not uniform across services - 71 known and 210 unknown `docker` step occurrences in audit).

## Related Links

- [[AwsEcr]] - push destination
- [[XbpReusableCi]] - main CI workflow
- [[XbpDevopsTools]] - host of the gradle-docker-build-push workflow
- [[github-actions-gradle-docker-push]] - the workflow doc that toggles Jib
- [[github-actions-build-workflow-concept]] - Jenkins migration concept that discusses Jib usage
