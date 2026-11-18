---
title: Declare the `GITHUB_TOKEN` permissions of every workflow
severity: error
applies_to: [".github/workflows/**"]
sources:
  - https://docs.github.com/en/actions/reference/security/secure-use#restricting-permissions-for-tokens
  - https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#permissions
---
## Check
Does the change add a workflow, or a job to a workflow, whose jobs get the `GITHUB_TOKEN`'s default permissions: no top-level `permissions:` and no `permissions:` on the job, or `permissions: write-all`?

## Violation
- An added workflow file with no top-level `permissions:` and at least one job without its own `permissions:`. Quote that job's id line (`build:`).
- An added job without `permissions:`, in a workflow that has no top-level `permissions:`. Quote the job's id line.
- An added `permissions: write-all`, at the top level or on a job.

The default comes from the repository or organisation setting, which can be read and write on every scope: the job then holds a token that can push, release or approve, and whatever runs in it (an action, a dependency's install script) can use it.

## Not a violation
- A top-level `permissions:`, even `permissions: {}` or `contents: read` with jobs that raise it.
- No top-level `permissions:`, but every job declares its own.
- `permissions: read-all`.
- An existing workflow or job the change edits without adding a job: only what the change adds is checked.
- Composite actions (`action.yml`): they have no `permissions:` key.

## Examples
```yaml
# ❌ Incorrect: an added workflow
on: pull_request
jobs:
  build:
    runs-on: ubuntu-26.04

# ✅ Correct
on: pull_request
permissions:
  contents: read
jobs:
  build:
    runs-on: ubuntu-26.04
  comment:
    permissions:
      pull-requests: write
    runs-on: ubuntu-26.04
```
