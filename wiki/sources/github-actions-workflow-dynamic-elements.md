---
title: "GitHub Actions Workflow Dynamic Elements"
type: source
tags: [devops, github-actions, reusable-workflow, parameterization, isrs-15738]
sources:
  - "[raw/devops/GitHub Actions Workflow Dynamic Elements - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166090734/GitHub+Actions+Workflow+Dynamic+Elements)"
last_updated: 2026-05-24
---

## Summary

Design for a single common reusable GitHub Actions workflow (ISRS-15738) that covers Java, Node, Python, and Go tech stacks via parameterization. Defines two required inputs (tech-stack and version) and optional inputs for custom build/test/integration-test/code-analysis commands, docker-image-name, artifact-repository, and feature toggles. The workflow auto-detects Java build tools (pom.xml -> Maven, build.gradle -> Gradle) and runs the appropriate build/test commands per tech-stack. Branch-aware defaults disable docker build and artifact publish for feature/PR branches.

## Key Claims

- Two required inputs: `tech-stack` (java|node|python|go) and `version` (string).
- Optional command overrides: build-command, test-command, integration-test-command, code-analysis-command - empty by default, fall back to tech-stack-specific defaults.
- Optional feature toggles default to true on main/master/dev: run-tag-generation, run-docker-build, run-publish-artifact.
- Optional defaults for other features: run-integration-test (false), run-code-analysis (false).
- Java build tool auto-detection: pom.xml -> Maven (`mvn clean install`); build.gradle/build.gradle.kts -> Gradle (`./gradlew build`).
- Tech-stack defaults: node -> npm install + npm run build / npm test / npm run integration-test; python -> pip install + python setup.py build / pytest / pytest --integration; go -> go build / go test / go test -tags=integration.
- Container registry default switches by branch: dev branch -> 217685855605.dkr.ecr (dev account); else -> 867289402855.dkr.ecr (prod account).

## Evidence and Notes

- Setup job uses a single shell step with if/elif on `inputs.tech-stack` to select the right `actions/setup-*` action.
- Tag generation step uses `TAG_NAME=v1.0.${{ github.run_number }}` and pushes a git tag.
- Build-and-push-dockerimage step uses `aws-actions/amazon-ecr-login@v1` with AWS_ACCESS_KEY_ID/SECRET_ACCESS_KEY/REGION secrets.
- Special tech-stack-specific workflows (e.g., Rust experiments) may exist before merging into the common reusable workflow.

## Related Links

- [[ReusableWorkflow]] - the artifact being designed
- [[GithubActions]] - underlying technology
- [[XbpReusableCi]] - implementation built on this design
- [[github-actions-build-workflow-concept]] - originating concept
- [[github-actions-blueprint-workflow]] - blueprint patterns applied
- [[github-actions-onboarding-guide]] - end-user guide
- [[AwsEcr]] - Docker push destination
