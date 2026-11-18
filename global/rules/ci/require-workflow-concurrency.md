---
title: Give every workflow a concurrency group
severity: warning
applies_to: [".github/workflows/**"]
sources:
  - https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#concurrency
  - https://docs.github.com/en/actions/how-tos/write-workflows/choose-when-workflows-run/control-workflow-concurrency
---
## Check
Does the change add a workflow file with no top-level `concurrency:` and at least one job without its own `concurrency:`?

## Violation
An added workflow file with neither a top-level `concurrency:` nor a `concurrency:` on each job. Quote its `on:` line, or the id line of a job without one. Without a group, every push starts another run next to the ones still running: they compete for runners, and an older run can finish last (a deployment, a comment, a published file overwritten by stale content).

## Not a violation
- A top-level `concurrency:`, whatever its group and `cancel-in-progress`.
- No top-level `concurrency:`, but every job has its own.
- A workflow triggered only by `workflow_call`: the caller's group applies, and `github.workflow` there is the caller's name, so a copy of the caller's group deadlocks.
- An existing workflow the change edits: only added workflow files are checked.

## Examples
```yaml
# ❌ Incorrect: an added workflow
on: pull_request
jobs:
  build:
    runs-on: ubuntu-26.04

# ✅ Correct: a new push cancels the run it replaces
on: pull_request
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
jobs:
  build:
    runs-on: ubuntu-26.04

# ✅ Correct: releases queue instead of cancelling each other
on:
  push:
    branches: [main]
concurrency:
  group: release
  cancel-in-progress: false
```
