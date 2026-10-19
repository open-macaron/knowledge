---
title: Don't use the non-null assertion `!`; narrow the value instead
severity: warning
applies_to: ["**/*.ts", "**/*.svelte"]
exclude: ["**/*.spec.ts", "**/tests/**", "**/__mocks__/**"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/17002#discussion_r3065674159
  - https://github.com/podman-desktop/podman-desktop/pull/17927#discussion_r3426434510
---
## Check
Does an added or modified line use a postfix non-null assertion `!` on an expression?

## Violation
A postfix `!` after an expression to drop `null` / `undefined` from its type: `map.get(key)!`, `this.provider!.status`, `items.find(…)!.id`, `element!.focus()`, `value!`. The ESLint rule `@typescript-eslint/no-non-null-assertion` isn't enabled, so lint won't catch it.

## Not a violation
- Logical negation `!value`, `!==`, `!=`.
- Definite assignment on a class field or variable, `private client!: Client;` (a different syntax; flag it only if the field could be initialized instead).
- Narrowing with `if (!x) { … return; }`, `??`, `?.`, or a thrown error before the use.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
filters.get(key)!.push(filter);

// ✅ Correct
const list = filters.get(key) ?? [];
list.push(filter);
filters.set(key, list);
```
