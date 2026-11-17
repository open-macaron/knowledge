---
title: Pin actions and reusable workflows to a full commit SHA
severity: error
applies_to: [".github/workflows/**", "**/action.yml", "**/action.yaml"]
sources:
  - https://docs.github.com/en/actions/reference/security/secure-use#using-third-party-actions
  - https://docs.github.com/en/actions/reference/security/secure-use#reusing-third-party-workflows
---
## Check
Does an added or modified `uses:` line reference an action or a reusable workflow from another repository by something other than a full 40-character commit SHA?

## Violation
An added or modified line such as `uses: actions/checkout@v7`, `uses: actions/setup-node@v7.0.1`, `uses: pnpm/action-setup@main`, `uses: some/action@latest` or `uses: org/repo/.github/workflows/build.yml@v2`: a tag, a branch or a short SHA can be moved to other code after review, which then runs with the job's token and secrets.

## Not a violation
- A full commit SHA, with or without a trailing version comment: `uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1`.
- A local action or workflow: `uses: ./.github/actions/setup`, `uses: ./.github/workflows/build.yml`.
- A container pinned by digest: `uses: docker://alpine@sha256:…`.
- Lines that were not added or modified by the change.

## Examples
```yaml
# ❌ Incorrect
- uses: actions/checkout@v7
- uses: actions/setup-node@main

# ✅ Correct
- uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
- uses: ./.github/actions/setup
```
