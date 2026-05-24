---
title: "semantic-release Tool"
type: concept
tags: [devops, versioning, automation, semver]
sources:
  - "[raw/devops/Github Actions Reusable Workflow Versioning - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166035845/Github+Actions+Reusable+Workflow+Versioning)"
last_updated: 2026-05-24
---

## Definition

`semantic-release` is the npm-based automation tool selected to manage versioning of GLS XB+ shared GitHub Actions workflows. It analyzes commit messages on push to `main`, determines the next SemVer (MAJOR.MINOR.PATCH) based on conventional commit prefixes, creates the corresponding git tag and GitHub release, and generates release notes. The `semantic-release-major-tag` plugin additionally maintains a floating major-version tag (e.g., `v1`) that always points to the latest release within that major series.

## Key Information

- npm install: `npm install --save-dev semantic-release semantic-release-major-tag`.
- Configured via `.releaserc` in the repo root.
- Plugin stack used: `@semantic-release/commit-analyzer`, `@semantic-release/release-notes-generator`, `@semantic-release/changelog`, `@semantic-release/npm`, `@semantic-release/github`, `@semantic-release/git`, `semantic-release-major-tag`.
- Triggered from a GitHub Actions release workflow on push to `main`; runs `npx semantic-release` with `GITHUB_TOKEN`.
- Maintains both standard tags (v1.0.0, v1.1.0, etc.) and a floating major tag (v1).
- Why selected: well-known, plugin ecosystem, integrates cleanly with GitHub Actions, satisfies the XB+ SemVer + floating-major requirement.

## Related Links

- [[SemanticVersioning]] - the underlying versioning scheme
- [[ReusableWorkflow]] - artifact being versioned
- [[XbpDevopsTools]] - the repository where this tool is configured
- [[github-actions-reusable-workflow-versioning]] - source document that chose this tool
- [[GithubActions]] - host platform
