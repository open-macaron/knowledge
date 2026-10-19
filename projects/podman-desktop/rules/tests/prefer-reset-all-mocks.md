---
title: Reset mocks with one vi.resetAllMocks() in the top-level beforeEach
severity: warning
applies_to: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/15671#discussion_r2685626198
  - https://github.com/podman-desktop/podman-desktop/pull/19077#discussion_r3932435760
  - https://github.com/podman-desktop/podman-desktop/pull/19300#discussion_r4037315125
  - https://github.com/podman-desktop/podman-desktop/pull/19043#discussion_r4104850198
  - https://github.com/podman-desktop/podman-desktop/blob/main/CODE-GUIDELINES.md#mock-a-complete-module
---
## Check
Does an added or modified line clean up mocks with something other than `vi.resetAllMocks()` in a `beforeEach`?

## Violation
An added or modified line that:
- calls `vi.clearAllMocks()` anywhere (it keeps the implementations set by earlier tests);
- calls `.mockClear()`, `.mockReset()` or `.mockRestore()` on a single mock or spy to clean up between tests (in a hook, or at the end of a test);
- calls `vi.resetAllMocks()`, `vi.clearAllMocks()` or `vi.restoreAllMocks()` in `beforeAll`, `afterEach` or `afterAll` instead of `beforeEach`.

## Not a violation
- `vi.resetAllMocks()` in a `beforeEach`, including a nested `describe`'s `beforeEach` that adds to the top-level one.
- `vi.restoreAllMocks()` in the top-level `beforeEach` next to `vi.resetAllMocks()`, when the file uses `vi.spyOn` (documented in CODE-GUIDELINES).
- `.mockReset()` in the middle of a test to change behaviour before a second call it then asserts on.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
beforeAll(() => {
  vi.clearAllMocks();
});
afterEach(() => {
  consoleErrorSpy.mockRestore();
});

// ✅ Correct
beforeEach(() => {
  vi.resetAllMocks();
});
```
