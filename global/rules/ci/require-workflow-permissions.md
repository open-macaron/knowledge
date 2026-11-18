---
title: Declare the `GITHUB_TOKEN` permissions of every workflow
severity: error
applies_to: [".github/workflows/**"]
sources:
  - https://docs.github.com/en/actions/reference/security/secure-use#restricting-permissions-for-tokens
  - https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#permissions
---
## Check
Does an added workflow or job run with the token's default permissions (no top-level `permissions:` and none on the job), or with `permissions: write-all`?

## Violation
An added workflow or job without either `permissions:` (quote the job's id line, e.g. `build:`), or an added `permissions: write-all`.

## Not a violation
- A top-level `permissions:` (even `{}`), or one on every job; `read-all`.
- Existing workflows and jobs the change edits.

## Examples
```yaml
# ❌
on: pull_request
jobs:
  build:
# ✅
on: pull_request
permissions:
  contents: read
jobs:
  build:
```
