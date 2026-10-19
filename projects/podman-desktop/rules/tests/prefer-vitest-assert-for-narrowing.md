---
title: Narrow possibly undefined values with assert(x) from vitest, not !, ?. or toBeDefined()
severity: warning
applies_to: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/16877#discussion_r3008341564
  - https://github.com/podman-desktop/podman-desktop/pull/16896#discussion_r3013924622
  - https://github.com/podman-desktop/podman-desktop/pull/18206#discussion_r3612660736
  - https://github.com/podman-desktop/podman-desktop/pull/19328#issuecomment-5910823650
---
## Check
Does an added or modified line get past a possibly `undefined`/`null` value with a non-null assertion `!`, an optional call or access `?.`, or a `toBeDefined()` / `not.toBeUndefined()` that doesn't narrow, instead of `assert(x)` from `vitest`?

## Violation
In a test, an added or modified line that:
- uses a non-null assertion on a value the test captured or looked up: `call![1]()`, `handler!.log('x')`, `mock.calls[0]![0]`;
- calls a captured callback with `?.`: `await capturedHandler?.log('x')`, `listener?.()` (the test passes silently if nothing was captured);
- writes `expect(x).toBeDefined()` (or `.not.toBeUndefined()`, `.toBeTruthy()`) and then uses `x` with `!` or `?.` on the next lines.

## Not a violation
- `assert(x)` / `assert(x, 'message')` imported from `vitest`, followed by plain use of `x`.
- `expect(x).toBeDefined()` when the test only checks existence and doesn't use `x` afterwards.
- `?.` in production code under test, or in an `expect(...)` on a value that may legitimately be absent (`expect(result?.name).toBe(...)`).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
const call = vi.mocked(window.events.receive).mock.calls.find(c => c[0] === 'extension-started');
expect(call).toBeDefined();
call![1]();
await capturedHandler?.log('x'); // passes even if the handler was never captured

// ✅ Correct
import { assert } from 'vitest';
const call = vi.mocked(window.events.receive).mock.calls.find(c => c[0] === 'extension-started');
assert(call);
call[1]();
assert(capturedHandler);
await capturedHandler.log('x');
```
