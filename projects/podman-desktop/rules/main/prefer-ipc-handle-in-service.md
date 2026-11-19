---
title: Register a new IPC handler in its service, not in plugin/index.ts
severity: warning
applies_to: ["packages/main/src/plugin/index.ts"]
---
## Check
Does the change add a `this.ipcHandle('…', …)` call to `packages/main/src/plugin/index.ts`?

## Violation
A new handler there: the ~300 existing ones aren't a precedent. The owning service injects `IPCHandle` (`/@/plugin/api.js`) and registers its handlers itself, as `feature-registry.ts` and `dashboard/dashboard-service.ts` do.

## Not a violation
- Edits to an existing handler, or moving handlers out of `index.ts`.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ in plugin/index.ts
this.ipcHandle('feature-registry:getRegisteredFeatures', async () => featureRegistry.getFeatures());
// ✅ in the service
constructor(@inject(IPCHandle) private readonly ipcHandle: IPCHandle) {}
init(): void {
  this.ipcHandle('feature-registry:getRegisteredFeatures', async () => this.getFeatures());
}
```
