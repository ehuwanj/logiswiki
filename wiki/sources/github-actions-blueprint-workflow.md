---
title: "GitHub Actions - Blueprint Workflow Definition"
type: source
tags: [devops, github-actions, architecture, reusable-workflow, composite-action]
sources:
  - "[raw/devops/Github Actions - Blueprint Workflow Definition - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166042083/Github+Actions+-+Blueprint+Workflow+Definition)"
last_updated: 2026-05-24
---

## Summary

Architectural blueprint for GitHub Actions adoption within GLS. Establishes two reuse primitives - Composite Actions (encapsulated multi-step actions, no Docker or JavaScript required) and Reusable Workflows (callable via `workflow_call`, parameterized, versioned). Recommends matrix builds, environment separation (dev/staging/prod), org-level secrets, manual triggers via `workflow_dispatch`, conditional execution, and `actions/cache`. The standard repository layout places workflows under `.github/workflows/`, custom composite actions under `.github/actions/<action-name>/action.yml`, and shared scripts under `scripts/`.

## Key Claims

- Composite Actions: YAML-only, support inputs/outputs/conditional logic, encapsulate reusable step bundles.
- Reusable Workflows: declared with `on: workflow_call`, called from other workflows via `uses:`, support inputs and parameterization.
- Workflows are stored in `.github/workflows/<file>.yml`; composite actions in `.github/actions/<name>/action.yml`.
- Org-level secrets, least-privilege workflow execution, and protected environments are required for production deployments.
- Recommended patterns: action reuse, matrix builds, environment separation, secrets management, manual triggers, conditional steps, caching.

## Evidence and Notes

- Composite action example: `setup-node/action.yml` declares `runs.using: "composite"` and contains steps that invoke `actions/setup-node@v4` and `npm ci`.
- Reusable workflow example: declares `on.workflow_call.inputs.node-version` (string, required), then a `test` job that checks out, uses the composite action, and runs `npm test`.
- Calling a reusable workflow: `uses: ./.github/workflows/reusable-ci.yml` with `with:` inputs.
- Docker/artifact credentials must be passed as secrets, never hardcoded.

## Related Links

- [[GithubActions]] - underlying technology
- [[ReusableWorkflow]] - reusable workflow concept
- [[CompositeAction]] - composite action concept
- [[github-actions-onboarding-guide]] - applied to GLS reusable CI
- [[github-actions-build-workflow-concept]] - Jenkins migration concept
- [[github-actions-workflow-dynamic-elements]] - dynamic input design
- [[github-actions-reusable-workflow-versioning]] - versioning the shared workflow repo
