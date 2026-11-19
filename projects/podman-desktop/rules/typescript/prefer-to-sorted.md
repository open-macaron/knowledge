---
title: Sort with toSorted(), not sort() on an array the code doesn't own
severity: warning
applies_to: ["packages/*/src/**/*.ts"]
exclude: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/blob/main/CODE-GUIDELINES.md
---
## Check
Does an added or modified line call `.sort(…)` on an array it didn't just create (a parameter, a field, a store value, a cached result)?

## Violation
`items.sort(compare)` or `this.providers.sort(…)`: `sort` mutates in place, so callers, stores and caches see their array reordered. (ESLint's `unicorn/no-array-sort` only covers `.svelte` files.)

## Not a violation
- `items.toSorted(compare)`.
- `.sort()` on a fresh copy or a freshly built array: `[...items].sort()`, `Array.from(set).sort()`, `items.map(…).sort()`.
- `extensions/`, which compile with `lib: ES2017` (out of scope).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌
return providers.sort((a, b) => a.name.localeCompare(b.name));
// ✅
return providers.toSorted((a, b) => a.name.localeCompare(b.name));
```
