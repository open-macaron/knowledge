---
title: Mock HTTP with msw, not nock or a real network call
severity: warning
applies_to: ["**/*.spec.ts"]
---
## Check
Does an added or modified line in a unit test import `nock`, or make a real HTTP request (an unmocked `fetch`, `got`, `http.request` to a real URL)?

## Violation
`import nock from 'nock'` (dropped from the repository), or a test that depends on a real server answering.

## Not a violation
- `msw`: `setupServer(http.get(url, () => HttpResponse.json(…)))`, as in `packages/main/src/plugin/image-registry.spec.ts`.
- A mocked client (`vi.mock(import('…'))`).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌
nock('https://registry.example').get('/v2/').reply(200);
// ✅
const server = setupServer(http.get('https://registry.example/v2/', () => HttpResponse.json({})));
```
