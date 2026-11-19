---
title: Extensions reach core only through @podman-desktop/api
severity: warning
applies_to: ["extensions/**/*.ts", "extensions/**/*.svelte"]
---
## Check
Does an added or modified import in an extension reach Podman Desktop's core code (`packages/…` by relative path, `@desktop-framework/*`, `/@api/`)?

## Violation
`import { … } from '../../../../packages/main/src/…'` or `from '@desktop-framework/api/…'`: extensions are built and shipped on their own and may only use the published API. ESLint doesn't cover `extensions/`.

## Not a violation
- `@podman-desktop/api`, `@podman-desktop/docker-extension-api`, `@podman-desktop/podman-extension-api`.
- Imports inside the extension itself.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌
import type { ProviderInfo } from '@desktop-framework/api/provider-info';
// ✅
import type { ProviderConnectionStatus } from '@podman-desktop/api';
```
