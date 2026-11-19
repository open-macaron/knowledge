---
title: Give every registered configuration property a default
severity: warning
applies_to: ["packages/main/src/**/*.ts"]
exclude: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/blob/43ab21a323e014a4f4429e219adc5ae071d7e39b/.agents/skills/review-podman-desktop/references/rules.md#L80
---
## Check
Does an added property in a configuration node passed to `registerConfigurations` lack a `default`?

## Violation
A property with `type` and `description` but no `default`: `get()` then returns `undefined` and every caller invents its own fallback. Quote the property's key line.

## Not a violation
- `default: false`, `default: ''`, `default: []`.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌
[`${TelemetrySettings.SectionName}.${TelemetrySettings.Enabled}`]: { type: 'boolean', description: '…' },
// ✅
[`${TelemetrySettings.SectionName}.${TelemetrySettings.Enabled}`]: { type: 'boolean', default: true, description: '…' },
```
