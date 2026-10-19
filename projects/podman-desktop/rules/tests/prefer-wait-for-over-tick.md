---
title: Wait for the DOM or a store with waitFor or findBy*, not await tick() or a polling loop
severity: warning
applies_to: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/15565#discussion_r2664603385
  - https://github.com/podman-desktop/podman-desktop/pull/15671#discussion_r2685641356
  - https://github.com/podman-desktop/podman-desktop/pull/19048#discussion_r3925403620
  - https://github.com/podman-desktop/podman-desktop/pull/19441#pullrequestreview-5487284774
  - https://github.com/podman-desktop/podman-desktop/blob/main/CODE-GUIDELINES.md#waitfor-vs-waituntil
---
## Check
Does an added or modified line wait for an asynchronous update with `tick()`, a sleep or a hand-written polling loop instead of `waitFor`, `vi.waitUntil` or a `findBy*` query?

## Violation
An added or modified line that:
- calls `await tick()` (from `svelte`), once or several times, before asserting on the DOM or a store;
- sleeps with `await new Promise(resolve => setTimeout(resolve, …))` (or a `sleep`/`wait` helper) to let the UI or a store settle;
- loops (`while`, `for`) re-reading a value until it changes.

## Not a violation
- `await vi.waitFor(() => expect(…))`, `await waitFor(…)` from `@testing-library/svelte`, `await vi.waitUntil(() => boolean)`, `await screen.findByRole(…)`.
- `await vi.advanceTimersByTimeAsync(…)` or `vi.runAllTimers()` with fake timers.
- `await userEvent.click(…)` / `await fireEvent.click(…)` (they already flush).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
render(PullImage);
await tick();
expect(screen.getByRole('button', { name: 'Pull' })).toBeEnabled();

while (get(searchPattern) === '') {
  await new Promise(resolve => setTimeout(resolve, 500));
}

// ✅ Correct
render(PullImage);
await vi.waitFor(() => expect(screen.getByRole('button', { name: 'Pull' })).toBeEnabled());

await vi.waitFor(() => expect(get(searchPattern)).toBe('test-search'));
```
