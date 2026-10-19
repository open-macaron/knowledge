---
title: Don't cast with `as T` without a runtime check; use an annotation, `satisfies` or a type guard
severity: warning
applies_to: ["**/*.ts", "**/*.svelte"]
exclude: ["**/*.spec.ts", "**/tests/**", "**/__mocks__/**"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/14459#discussion_r2493585147
  - https://github.com/podman-desktop/podman-desktop/pull/14782#discussion_r2502176433
  - https://github.com/podman-desktop/podman-desktop/pull/16111#discussion_r3014262733
  - https://github.com/podman-desktop/podman-desktop/pull/19441#pullrequestreview-5487284774
---
## Check
Does an added or modified line assert a type with `as T` (or `<T>value`) where nothing at runtime checks that the value really has that type?

## Violation
- An object literal cast to its type: `{ … } as NetworkContainer`, `{} as Request`, `[] as string[]`.
- A cast of a value of unknown shape (an `unknown`, `any`, a parsed JSON, an indexed configuration value, an IPC or API payload): `lockedConfig?.['locked'] as string[]`, `JSON.parse(text) as Settings`.
- A double cast `as unknown as T`.

## Not a violation
- `as const`.
- A cast right after a guard on the same value (`typeof`, `Array.isArray`, `instanceof`, `'key' in value`, a Zod `parse`) that already proves the type.
- A type annotation (`const x: T = …`) or `satisfies T`: the compiler checks those.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
return { name: container.Name, id } as NetworkContainer;
let request: Request = {} as Request;
const locked = lockedConfig?.['locked'] as string[];

// ✅ Correct
return { name: container.Name, id } satisfies NetworkContainer;
let request: Request | undefined;
const locked = Array.isArray(lockedConfig?.['locked']) ? lockedConfig['locked'] : [];
```
