---
title: Automock a module with vi.mock(import('…')) alone when the factory only returns vi.fn()s
severity: warning
applies_to: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/14908#discussion_r2533575123
  - https://github.com/podman-desktop/podman-desktop/pull/14316#discussion_r2412848062
  - https://github.com/podman-desktop/podman-desktop/pull/18196#discussion_r3542624452
  - https://github.com/podman-desktop/podman-desktop/pull/19077#discussion_r3932439491
  - https://github.com/podman-desktop/podman-desktop/blob/main/CODE-GUIDELINES.md#mock-a-complete-module
---
## Check
Does an added or modified `vi.mock(…)` call pass a factory whose returned object only maps exports to bare `vi.fn()` (or `vi.fn()` with an implementation that could be set with `vi.mocked`)?

## Violation
An added or modified line such as `vi.mock(import('node:fs'), () => ({ readFileSync: vi.fn(), existsSync: vi.fn() }))`, `vi.mock('/@/navigation', () => ({ handleNavigation: vi.fn() }))`, or a factory returning `{ default: { foo: vi.fn() } }` or `{ Foo: vi.fn() }` for a class. Vitest's automock already replaces every function and class method with a `vi.fn()`; behaviour belongs in `vi.mocked(fn).mock…()` in the test or `beforeEach`.

## Not a violation
- Partial mocks with `importOriginal`: `vi.mock(import('…'), async importOriginal => ({ ...(await importOriginal()), foo: vi.fn() }))`.
- Factories that return real values, constants, stores (`writable(…)`) or mock Svelte components.
- `vi.mock(import('…'))` with no factory.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
vi.mock(import('node:fs'), () => ({ readFileSync: vi.fn(), existsSync: vi.fn() }));
vi.mock(import('/@/navigation'), () => ({ handleNavigation: vi.fn() }));

// ✅ Correct
vi.mock(import('node:fs'));
vi.mock(import('/@/navigation'));

beforeEach(() => {
  vi.mocked(readFileSync).mockReturnValue('content');
});
```
