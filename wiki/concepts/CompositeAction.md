---
title: "Composite Action"
type: concept
tags: [devops, github-actions, composite-action, ci-cd]
sources:
  - "[raw/devops/Github Actions - Blueprint Workflow Definition - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042083/Github+Actions+-+Blueprint+Workflow+Definition)"
  - "[raw/devops/Github Actions Build Workflow - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166089345/Github+Actions+Build+Workflow)"
last_updated: 2026-05-24
---

## Definition

A Composite Action is a GitHub Actions custom action declared as a YAML file at `.github/actions/<action-name>/action.yml` that bundles multiple steps into a single reusable unit. Unlike Docker or JavaScript actions, composite actions are pure YAML and need no runtime or build step. They support inputs, outputs, and conditional logic via `if:`. In GLS XB+, composite actions wrap helper logic (e.g., notification URL determination) that was previously implemented in the Jenkins shared library.

## Key Information

**Syntax:**
```yaml
name: 'Setup Node'
description: 'Setup Node.js and install dependencies'
inputs:
  node-version:
    required: true
    description: 'Node.js version'
runs:
  using: "composite"
  steps:
    - uses: actions/setup-node@v4
      with:
        node-version: ${{ inputs.node-version }}
    - run: npm ci
      shell: bash
```

**Composite action vs reusable workflow:**
- Composite action lives inside a job and replaces a sequence of steps.
- Reusable workflow ([[ReusableWorkflow]]) replaces an entire job (or set of jobs) and runs on its own runner.
- Choose composite action for tight step bundles; choose reusable workflow for orchestration across jobs.

**Migration role:**
- Replaces unsupported Jenkins shared-library helpers (e.g., `cleanworkspace`, `getNotificationUrl`) flagged by the GitHub Actions Importer.
- Hosted alongside reusable workflows in [[XbpDevopsTools]].

## Related Links

- [[ReusableWorkflow]] - alternative reuse abstraction
- [[GithubActions]] - host platform
- [[XbpDevopsTools]] - hosting repository
- [[github-actions-blueprint-workflow]] - architectural source
- [[github-actions-build-workflow-concept]] - originating Jenkins migration concept
