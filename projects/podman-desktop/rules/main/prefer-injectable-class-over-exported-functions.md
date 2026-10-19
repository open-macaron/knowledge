---
title: Put new service logic in an `@injectable()` class, not in exported functions or module-level state
severity: warning
applies_to: ["packages/main/src/**/*.ts", "extensions/podman/packages/extension/src/**/*.ts"]
exclude: ["**/*.spec.ts", "packages/main/src/plugin/util/**"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/18220#pullrequestreview-4807589928
  - https://github.com/podman-desktop/podman-desktop/pull/19302#pullrequestreview-5236630647
  - https://github.com/podman-desktop/podman-desktop/pull/19314#pullrequestreview-5246393255
  - https://github.com/podman-desktop/podman-desktop/pull/19043#discussion_r4102988005
---
## Check
Does the change add an exported function, or an exported / module-level constant or mutable variable, that carries service logic or state instead of a method or field of an `@injectable()` class (inversify)?

## Violation
- An added `export function detectX(…)` or `export const doX = (…) => …` in a new or service module, used by other services.
- An added module-level `export const SOME_LIST = [...]` or `let cache = new Map()` that a service reads or mutates.
- A new module with only free functions where a class decorated `@injectable()` and bound in the container would do.

## Not a violation
- Zod schemas, `export type` / `export interface`, enums and symbols used as inversify identifiers.
- Small pure helpers in `util/` / `utils/` modules.
- A `static readonly` constant or a method inside an `@injectable()` class.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
export const CI_ENVIRONMENT_VARIABLES = ['CI', 'GITHUB_ACTIONS'];
export function detectCIEnvironment(env = process.env): boolean { … }

// ✅ Correct
import { injectable } from 'inversify';

@injectable()
export class CIDetection {
  protected static readonly VARIABLES = ['CI', 'GITHUB_ACTIONS'];
  isCI(env = process.env): boolean { … }
}
```
