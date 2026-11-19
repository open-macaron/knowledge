---
title: No NodeJS types in renderer and UI code; setTimeout returns a number there
severity: warning
applies_to: ["packages/renderer/src/**", "packages/ui/src/**"]
exclude: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/blob/43ab21a323e014a4f4429e219adc5ae071d7e39b/.agents/skills/review-podman-desktop/references/rules.md#L50
---
## Check
Does an added or modified line use a `NodeJS.*` type (`NodeJS.Timeout`) or work around it with `ReturnType<typeof setTimeout>` / `ReturnType<typeof setInterval>`?

## Violation
Either form: the renderer runs in a browser context where timers return `number`; Node types only leak in through test type packages, so fix the tsconfig libs rather than the code.

## Not a violation
- `let timer: number | undefined`.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌
let timeout: NodeJS.Timeout | undefined;
let interval: ReturnType<typeof setInterval>;
// ✅
let timeout: number | undefined;
```
