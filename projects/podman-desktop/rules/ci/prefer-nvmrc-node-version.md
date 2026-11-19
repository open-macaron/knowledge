---
title: Read the Node.js version from .nvmrc in workflows
severity: warning
applies_to: [".github/workflows/**", "**/action.yml", "**/action.yaml"]
sources:
  - https://github.com/podman-desktop/podman-desktop/blob/43ab21a323e014a4f4429e219adc5ae071d7e39b/.agents/skills/review-podman-desktop/references/rules.md#L129
---
## Check
Does an added or modified `actions/setup-node` step set `node-version:` instead of `node-version-file: .nvmrc`?

## Violation
`node-version: 24` or `node-version: 'lts/*'`: the workflow drifts from the version the project builds with.

## Not a violation
- `node-version-file: .nvmrc`.
- Lines that were not added or modified by the change.

## Examples
```yaml
- uses: actions/setup-node@…
  with:
    node-version: 24            # ❌
    node-version-file: .nvmrc   # ✅
```
