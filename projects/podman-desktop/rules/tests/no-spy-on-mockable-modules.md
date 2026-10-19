---
title: Automock a module or class instead of adding vi.spyOn on it, its prototype or console
severity: warning
applies_to: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/19441#discussion_r4204373162
  - https://github.com/podman-desktop/podman-desktop/pull/19625#pullrequestreview-5500271323
  - https://github.com/podman-desktop/podman-desktop/pull/14316#discussion_r2412855679
  - https://github.com/podman-desktop/podman-desktop/pull/19501
  - https://github.com/podman-desktop/podman-desktop/blob/main/CODE-GUIDELINES.md#mock-complete-modules-spy-on-parts-of-module-for-specific-tests
---
## Check
Does an added or modified line call `vi.spyOn` on an imported module, a class prototype or `console` where `vi.mock(import('…'))` plus `vi.mocked` would do?

## Violation
An added or modified line such as:
- `vi.spyOn(router, 'goto')`, `vi.spyOn(someModule, 'fn')` on an import whose real implementation the test doesn't need;
- `vi.spyOn(SomeClass.prototype, 'method')` when the class's module can be automocked;
- `vi.spyOn(console, 'error')` / `'log'` / `'warn'` just to silence or assert output (mock the logger or `console` methods with `vi.fn()` in a `beforeEach` instead, or assert on the behaviour).

## Not a violation
- The documented exception (CODE-GUIDELINES "Spy on a function for a specific test"): spying on one or a few functions of a module whose other real functions the tests keep using, e.g. a function of the module under test or a helper module, restored with `vi.restoreAllMocks()` in the top-level `beforeEach`.
- Spying on an instance the test created (`vi.spyOn(myService, 'refresh')` on `new MyService()`), or on DOM/browser objects (`window`, `HTMLElement.prototype`, `navigator.clipboard`).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
const gotoSpy = vi.spyOn(router, 'goto');
vi.spyOn(KubernetesClient.prototype, 'getContexts').mockReturnValue([]);

// ✅ Correct
vi.mock(import('tinro'));
vi.mock(import('/@/plugin/kubernetes/kubernetes-client'));
vi.mocked(KubernetesClient.prototype.getContexts).mockReturnValue([]);
expect(router.goto).toHaveBeenCalledWith('/pods');
```
