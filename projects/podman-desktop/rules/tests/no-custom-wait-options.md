---
title: Keep the default interval and timeout of waitFor, vi.waitFor and vi.waitUntil
severity: warning
applies_to: ["**/*.spec.ts"]
---
## Check
Does an added or modified line pass an `interval` or `timeout` option to `waitFor`, `vi.waitFor` or `vi.waitUntil`?

## Violation
`await vi.waitFor(() => …, { interval: 10 })`, `waitFor(() => …, { timeout: 5000 })`: short intervals turn slow CI runners into flakes, long timeouts hide hangs. Test extracted logic or use fake timers instead.

## Not a violation
- The timeout of a test itself: `test('…', { timeout: 20_000 }, …)`.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌
await vi.waitFor(() => expect(list).toHaveLength(2), { interval: 10, timeout: 100 });
// ✅
await vi.waitFor(() => expect(list).toHaveLength(2));
```
