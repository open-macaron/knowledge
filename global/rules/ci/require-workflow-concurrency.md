---
title: Give every workflow a concurrency group
severity: warning
applies_to: [".github/workflows/**"]
sources:
  - https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#concurrency
  - https://docs.github.com/en/actions/how-tos/write-workflows/choose-when-workflows-run/control-workflow-concurrency
---
## Check
Does an added workflow file have no top-level `concurrency:` and a job without its own?

## Violation
Such a file; quote its `on:` line.

## Not a violation
- Any top-level `concurrency:`, or one on every job.
- A workflow triggered only by `workflow_call` (it runs in its caller's group).
- Existing workflows the change edits.

## Examples
```yaml
# ✅
on: pull_request
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```
