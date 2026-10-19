---
title: Import modules at the top of the spec file, not with await import() in a test or hook
severity: warning
applies_to: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/14246#discussion_r2426080337
  - https://github.com/podman-desktop/podman-desktop/pull/14316#discussion_r2412849285
  - https://github.com/podman-desktop/podman-desktop/pull/16378#discussion_r2871692831
  - https://github.com/podman-desktop/podman-desktop/pull/17615#discussion_r3302424366
---
## Check
Does an added or modified line load a module with `await import('…')` inside a `test`/`it`, a `describe` or a hook (`beforeEach`, `beforeAll`, …) instead of a static `import` at the top of the file?

## Violation
An added or modified line such as `const { isMac } = await import('/@/util');` or `const mod = await import('./my-module');` inside a test body or hook, used to reach a function that `vi.mock(import('…'))` (hoisted) plus a top-level `import` would give.

## Not a violation
- `await import(…)` right after `vi.resetModules()` (or `vi.doMock(…)`), to get a fresh module instance with different module-level state.
- `vi.mock(import('…'))` and `importOriginal` / `await importOriginal()` inside a `vi.mock` factory.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
test('uses mac settings', async () => {
  const { isMac } = await import('/@/util');
  vi.mocked(isMac).mockReturnValue(true);
});

// ✅ Correct
import { isMac } from '/@/util';

vi.mock(import('/@/util'));

test('uses mac settings', () => {
  vi.mocked(isMac).mockReturnValue(true);
});
```
