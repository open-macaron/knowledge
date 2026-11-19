---
title: Pass $state.snapshot(x), not a $state proxy, to window.* calls
severity: warning
applies_to: ["packages/renderer/src/**/*.svelte", "packages/renderer/src/**/*.svelte.ts"]
---
## Check
Does an added or modified line pass an object or array declared with `$state` (the declaration is in the diff or the same file) directly as an argument of a `window.*` call?

## Violation
`window.createSecret(options)` where `let options = $state({…})`: $state objects are Proxies, and Electron's IPC (structured clone) throws "An object could not be cloned" (see `lib/kube/details/KubePort.svelte`).

## Not a violation
- `window.createSecret($state.snapshot(options))`.
- Primitives (`string`, `number`, `boolean`) held in `$state`, and `$derived` values built as plain objects.
- A variable whose declaration you can't see: don't guess.
- Lines that were not added or modified by the change.

## Examples
```ts
let options = $state<SecretCreateOptions>({ name: '' });
// ❌
await window.createSecret(options);
// ✅
await window.createSecret($state.snapshot(options));
```
