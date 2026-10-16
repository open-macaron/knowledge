---
title: No console.log or console.debug in shipped code
severity: warning
applies_to: ["packages/*/src/**/*.ts", "packages/*/src/**/*.svelte"]
exclude: ["**/*.spec.ts", "**/*.test.ts"]
---
## Check
Does the changed code leave a `console.log` or `console.debug` call in code that ships with the extension?

## Violation
An added or modified line calling `console.log(…)` or `console.debug(…)` outside tests.

## Not a violation
- `console.error(…)` and `console.warn(…)` reporting a real failure.
- Calls in `*.spec.ts` / `*.test.ts` files.
- Lines that were not added or modified by the change.

## Examples
```ts
// ✅
console.error('failed to read the quadlet', err);

// ❌
console.log('content', content);
```
