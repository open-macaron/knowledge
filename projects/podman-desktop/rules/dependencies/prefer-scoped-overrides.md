---
title: Scope a new pnpm override to its parent or to a version range
severity: warning
applies_to: ["pnpm-workspace.yaml"]
sources:
  - https://github.com/podman-desktop/podman-desktop/blob/43ab21a323e014a4f4429e219adc5ae071d7e39b/.agents/skills/review-podman-desktop/references/rules.md#L109
---
## Check
Does the change add an entry under `overrides:` with a bare package name (`qs: ^6.14.1`)?

## Violation
A bare name forces the version on every package that depends on it, including those that need another major.

## Not a violation
- `parent>dep` (`extract-zip>yauzl: ^3.3.1`) or a range selector (`minimatch@^9: ^9.0.7`).
- Existing bare entries whose version is only bumped.
- Lines that were not added or modified by the change.

## Examples
```yaml
overrides:
  path-to-regexp: ^3.3.0                     # ❌
  serve-handler>path-to-regexp: ^3.3.0       # ✅
```
