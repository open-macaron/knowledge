---
title: Don't cast what the mock's types already give; annotate or let vi.mocked type it
severity: warning
applies_to: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/15514#discussion_r2655199094
  - https://github.com/podman-desktop/podman-desktop/pull/18206#pullrequestreview-4664131151
  - https://github.com/podman-desktop/podman-desktop/pull/19028#discussion_r3895185023
---
## Check
Does an added or modified line add an `as` cast (or `any`) to a mock, a mock argument or a fixture where the type is already known or an annotation would do?

## Violation
An added or modified line that:
- casts a `vi.fn()` to its target type: `vi.fn() as IPCHandle`, `vi.fn() as unknown as typeof foo` (annotate the variable instead: `const ipcHandle: IPCHandle = vi.fn()`);
- casts a parameter that `vi.mocked(…).mockImplementation(…)` already types: `(func as () => void)()`, `(args[0] as string)`;
- casts to `any`, `as any`, or `as unknown as any` for a fixture or mock;
- wraps something in `vi.mocked` and also casts it: `(vi.mocked(fn) as Mock).mockReturnValue(…)`.

## Not a violation
- `{ … } as unknown as BigType` for a deliberately partial fixture of a large interface or class (the house style, about 2,000 uses on main).
- `as const` on literals, and `satisfies`.
- Casts needed because the value really has a wider type (e.g. `JSON.parse` results, `unknown` errors in `catch`).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
const ipcHandle = vi.fn() as IPCHandle;
vi.mocked(window.events.receive).mockImplementation((_channel, func) => {
  (func as () => void)();
  return { dispose: vi.fn() };
});
const extension = { id: 'x' } as any;

// ✅ Correct
const ipcHandle: IPCHandle = vi.fn();
vi.mocked(window.events.receive).mockImplementation((_channel, func) => {
  func();
  return { dispose: vi.fn() };
});
const extension = { id: 'x' } as unknown as ExtensionInfo;
```
