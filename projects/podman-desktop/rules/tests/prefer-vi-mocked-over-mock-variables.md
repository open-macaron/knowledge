---
title: Drive mocks through vi.mocked(fn), not a module-level xMock = vi.fn() variable
severity: warning
applies_to: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/14532#discussion_r2459291833
  - https://github.com/podman-desktop/podman-desktop/pull/14316#discussion_r2412857678
  - https://github.com/podman-desktop/podman-desktop/pull/15605#discussion_r2675261288
  - https://github.com/podman-desktop/podman-desktop/pull/18337#discussion_r3813907839
  - https://github.com/podman-desktop/podman-desktop/blob/main/CODE-GUIDELINES.md#use-vimocked-not-a-generic-myfunctionmock
---
## Check
Does an added or modified line declare a standalone `vi.fn()` variable that is then wired into a `vi.mock` factory, a fake object or `window`, and configured or asserted through that variable?

## Violation
An added or modified line such as `const fooMock = vi.fn();` (or `let`, or `vi.hoisted(() => vi.fn())`) whose variable is then placed in a `vi.mock(…, () => ({ foo: fooMock }))` factory, in an object literal (`{ createTask: createTaskMock }`), or on `window`, and later used as `fooMock.mockReturnValue(…)` or `expect(fooMock)`. Such a variable is typed `Mock<Procedure>`, so TypeScript can't check the values given to it.

## Not a violation
- Callbacks passed as props or arguments: `const onClose = vi.fn(); render(Dialog, { onClose });`.
- `vi.fn()` written inline in the fake object, then driven with `vi.mocked(obj.method)`.
- `const spy = vi.spyOn(…)` (another rule covers spies).
- A typed variable such as `const ipcHandle: IPCHandle = vi.fn();` used as a whole argument.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
const goBackMock = vi.fn();
vi.mock(import('/@/stores/navigation-history.svelte'), () => ({ goBack: goBackMock }));

const createTaskMock = vi.fn();
const taskManager = { createTask: createTaskMock } as unknown as TaskManager;
createTaskMock.mockReturnValue(task);

// ✅ Correct
import { goBack } from '/@/stores/navigation-history.svelte';
vi.mock(import('/@/stores/navigation-history.svelte'));
expect(goBack).toHaveBeenCalled();

const taskManager = { createTask: vi.fn() } as unknown as TaskManager;
vi.mocked(taskManager.createTask).mockReturnValue(task);
```
