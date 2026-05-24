---
title: "GitHub Actions Reusable Workflow Versioning"
type: source
tags: [devops, github-actions, versioning, semver, isrs-15740]
sources:
  - "[raw/devops/Github Actions Reusable Workflow Versioning - CrossBorder+.md](https://gls-group.atlassian.net/wiki/spaces/CROS/pages/166035845/Github+Actions+Reusable+Workflow+Versioning)"
last_updated: 2026-05-24
---

## Summary

Strategy decision (ISRS-15740) for versioning the shared reusable GitHub Actions workflows. Five approaches were evaluated: Semantic Versioning (SemVer), Calendar Versioning (CalVer), Incremental, Commit-Based, and Feature-Based. Recommendation: SemVer (MAJOR.MINOR.PATCH) plus a floating major-version tag (e.g., `v1`) that always points to the latest release within that major series. The shared workflows live in a dedicated repository with `docs/`, `tests/`, `src/scripts/`, and `.github/workflows/`. Versioning is automated via `semantic-release` with the `semantic-release-major-tag` plugin. No LTS versions; deprecation policy is two-sprint advance notice via XB+ Platform Products Teams channel.

## Key Claims

- SemVer chosen over CalVer/Incremental/Commit/Feature versioning - chosen for clear communication of breaking vs. backward-compatible changes.
- Floating major tag (e.g., `v1`) added on top of standard X.Y.Z tags for consumers who want auto-updates within a major series.
- Release-branch flow preferred over commit-to-main-each-release because developers are more familiar with it.
- Dedicated repository for shared workflows recommended over co-locating with consumer projects: centralized management, clear separation, access control, reusability, testing.
- Versioning policy: no LTS releases; consumers may pin any version; for in-flight features, consumers point to the `dev` branch or a feature branch.
- Deprecation policy: 2 sprints notice via XB+ Platform Products Channel in Teams; documented in changelog with migration guide.
- Tooling: `semantic-release` + `semantic-release-major-tag` plugin automates version bumps based on commit messages.

## Evidence and Notes

- Repo layout: `.github/workflows/` (CI/CD/test workflows), `docs/` (CHANGELOG, CONTRIBUTING, MIGRATION_GUIDE), `tests/` (test workflow files), `src/scripts/` (setup.sh, deploy.sh), `README.md`.
- `.releaserc` configures branches: ["main"] and plugins: commit-analyzer, release-notes-generator, changelog, npm, github, git, semantic-release-major-tag.
- Release workflow runs on push to main, installs `semantic-release` via npm, runs `npx semantic-release` with `GITHUB_TOKEN`.
- Consumers reference workflows via fixed tags (e.g., `@v1.0.0`) or via the floating major tag (e.g., `@v1`).
- Reference: https://docs.github.com/en/actions/sharing-automations/reusing-workflows#calling-a-reusable-workflow

## Related Links

- [[SemanticReleaseTool]] - tooling used to automate versioning
- [[ReusableWorkflow]] - the artifact being versioned
- [[XbpDevopsTools]] - the dedicated repository
- [[GithubActions]] - underlying technology
- [[SemanticVersioning]] - underlying versioning scheme
- [[github-actions-blueprint-workflow]] - blueprint motivating the versioning need
- [[github-actions-build-workflow-concept]] - originating Jenkins-migration concept
