---
title: Configure the preload window methods with vi.mocked, don't redefine them
severity: warning
applies_to: ["packages/renderer/**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/14703#discussion_r2482805224
  - https://github.com/podman-desktop/podman-desktop/pull/15671#discussion_r2685636138
  - https://github.com/podman-desktop/podman-desktop/pull/16313#discussion_r2839652839
  - https://github.com/podman-desktop/podman-desktop/pull/19077#discussion_r3932340425
---
## Check
Does an added or modified line redefine a preload method on `window` that `packages/renderer/vite.tests.setup.ts` already mocks with `vi.fn()`?

## Violation
`vite.tests.setup.ts` replaces every function of the preload `Window` interface (`getConfigurationValue`, `openExternal`, `listContainers`, `telemetryTrack`, …) with a `vi.fn()` for every renderer test, and `window.events` with `{ send: vi.fn(), receive: vi.fn() }`. Flag an added or modified line that:
- calls `Object.defineProperty(window, '<preloadMethod>', { value: vi.fn()… })`;
- assigns `window.<preloadMethod> = vi.fn()`, including through a cast such as `(window as any).openExternal = vi.fn()` or `(window as unknown as { … }).x = …`;
- uses `vi.stubGlobal` or `Object.defineProperty(global, 'window', …)` to provide preload methods.

## Not a violation
- `vi.mocked(window.getConfigurationValue).mockResolvedValue(false)` and `expect(window.openExternal).toHaveBeenCalled…`.
- Non-preload globals: `matchMedia`, `ResizeObserver`, `navigator`, `localStorage`, `IntersectionObserver`, `HTMLElement.prototype.*`.
- Redefining `window.events` when the test must intercept `send` (the setup file allows it).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
Object.defineProperty(window, 'getConfigurationValue', { value: vi.fn().mockResolvedValue(false) });
(window as unknown as { openExternal: unknown }).openExternal = vi.fn();

// ✅ Correct
vi.mocked(window.getConfigurationValue).mockResolvedValue(false);
expect(window.openExternal).toHaveBeenCalledWith('https://podman-desktop.io');
```
