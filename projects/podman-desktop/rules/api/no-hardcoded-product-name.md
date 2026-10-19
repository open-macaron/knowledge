---
title: Take the product name from product.json in packages/main, never hardcode "Podman Desktop"
severity: warning
applies_to: ["packages/main/src/**/*.ts", "packages/preload/src/**/*.ts", "packages/renderer/src/**/*.ts", "packages/renderer/src/**/*.svelte", "packages/ui/src/**/*.svelte"]
exclude: ["**/*.spec.ts", "**/*.stories.svelte"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/17186#discussion_r3117911915
  - https://github.com/podman-desktop/podman-desktop/pull/17106#discussion_r3096064257
  - https://github.com/podman-desktop/podman-desktop/pull/15536#pullrequestreview-3625955577
  - https://github.com/podman-desktop/podman-desktop/pull/15924#issuecomment-3811599250
---
## Check
Does an added or modified line hardcode the product name (`Podman Desktop`, `podman-desktop`, `podman-desktop://`) in a user-visible string, a storage key or a URL protocol, or import `product.json` outside `packages/main`?

## Violation
- A string literal or template containing `Podman Desktop` in dialogs, notifications, labels, `alt`/`title` attributes, error messages or logs.
- New keys or identifiers built from the product id: `'podman-desktop-last-route'`, `'podman-desktop://…'` (use `product.artifactName` / `product.urlProtocol`).
- `import product from '/@product.json'` in `packages/renderer` or `packages/ui`: the renderer receives ready-made strings from main through IPC, never the name itself.

## Not a violation
- In `packages/main`: `import product from '/@product.json' with { type: 'json' };` then `product.name`, `product.artifactName`, `product.urlProtocol`.
- Mentions of the Podman *engine* or CLI (`Podman machine`, `podman`, `Podman` provider), and links to `podman-desktop.io` docs.
- Comments, specs and stories.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
await dialog.showMessageBox({ title: 'Restart Podman Desktop?' });
const LAST_ROUTE_KEY = 'podman-desktop-last-route';
import product from '/@product.json'; // in packages/renderer

// ✅ Correct (packages/main)
import product from '/@product.json' with { type: 'json' };
await dialog.showMessageBox({ title: `Restart ${product.name}?` });
const LAST_ROUTE_KEY = `${product.artifactName}-last-route`;
```
