---
title: Mark a parameter, property or prop that can be omitted with `?`, not `T | undefined`
severity: warning
applies_to: ["**/*.ts", "**/*.svelte"]
exclude: ["**/*.spec.ts", "**/tests/**", "**/__mocks__/**"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/16423#discussion_r2879208591
  - https://github.com/podman-desktop/podman-desktop/pull/19158#discussion_r3976029086
  - https://github.com/podman-desktop/podman-desktop/pull/18610#discussion_r3726961544
---
## Check
Does an added or modified line declare a trailing function parameter, an interface or type property, or a Svelte `Props` field as `name: T | undefined` instead of `name?: T`?

## Violation
- A trailing parameter typed `| undefined` without `?`: `function connect(host: string, identity: string | undefined)`.
- An interface, type alias or `Props` member typed `| undefined` without `?`: `interface Props { pvc: V1PersistentVolumeClaim | undefined }`.

## Not a violation
- A non-trailing parameter that callers must pass explicitly (`f(a: string | undefined, b: number)`).
- A variable or return type `T | undefined` (`let x: string | undefined;`, `): Foo | undefined`).
- `name?: T` alone (writing `name?: T | undefined` is already an error of `redundant-undefined/redundant-undefined`).
- A property that must be present as a key, documented as such (e.g. a value serialized with the key kept).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
function connect(host: string, identity: string | undefined): void {}
interface Props { pvc: V1PersistentVolumeClaim | undefined }

// ✅ Correct
function connect(host: string, identity?: string): void {}
interface Props { pvc?: V1PersistentVolumeClaim }
```
