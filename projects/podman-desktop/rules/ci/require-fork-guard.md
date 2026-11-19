---
title: Skip jobs on forks with github.event.repository.fork == false
severity: warning
applies_to: [".github/workflows/**"]
sources:
  - https://github.com/podman-desktop/podman-desktop/blob/43ab21a323e014a4f4429e219adc5ae071d7e39b/.agents/skills/review-podman-desktop/references/rules.md#L129
---
## Check
Does an added job, in a workflow triggered by `push`, `schedule`, `release`, `workflow_run` or `issue_comment`, lack a fork guard in its `if:`? Quote the job's id line.

## Violation
Such a job without `github.event.repository.fork == false`: forks (including bots working from forks) run these triggers too, and the job fails without secrets or burns their minutes.

## Not a violation
- `if: github.event.repository.fork == false` (alone or with `&&`), or `github.repository == 'podman-desktop/podman-desktop'`.
- Workflows triggered only by `pull_request`, `merge_group`, `workflow_dispatch` or `workflow_call`.
- Lines that were not added or modified by the change.

## Examples
```yaml
jobs:
  tag:
    # ✅ (next-build.yaml)
    if: github.event.repository.fork == false
```
