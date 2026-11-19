---
title: Justify every package allowed to run install scripts
severity: warning
applies_to: ["pnpm-workspace.yaml"]
sources:
  - https://github.com/podman-desktop/podman-desktop/blob/43ab21a323e014a4f4429e219adc5ae071d7e39b/.agents/skills/review-podman-desktop/references/rules.md#L109
---
## Check
Does the change add an entry with `true` under `allowBuilds:` (or an `onlyBuiltDependencies` entry) without saying, in a comment or the PR description, why the package must build?

## Violation
`allowBuilds: { foo: true }` added without a reason: pnpm blocks dependency install scripts by default, and `true` lets that package run code on every install. Packages shipping prebuilt binaries in optional dependencies don't need it.

## Not a violation
- A `false` entry (silences the warning, keeps scripts blocked), as for `@swc/core`.
- A `true` entry with a comment saying what breaks without the script.
- Lines that were not added or modified by the change.

## Examples
```yaml
allowBuilds:
  some-native-addon: true  # ❌ no reason
  '@swc/core': false       # ✅ prebuilt binaries
```
