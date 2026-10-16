---
title: Use @podman-desktop/ui-svelte components for common controls
severity: warning
applies_to: ["**/*.svelte"]
sources:
  - https://www.npmjs.com/package/@podman-desktop/ui-svelte
---
## Check
Does the changed code build a button, text input, checkbox or error message from raw HTML elements where `@podman-desktop/ui-svelte` provides a component?

## Violation
An added or modified line with a raw `<button>`, `<input type="text">`, `<input type="checkbox">`, or a hand-styled error message element, in a place where `Button`, `Input`, `Checkbox` or `ErrorMessage` from `@podman-desktop/ui-svelte` would do the same job.

## Not a violation
- Components that wrap or extend a `@podman-desktop/ui-svelte` component, or controls it doesn't provide.
- Raw elements inside tests or stories.
- Lines that were not added or modified by the change.

## Examples
```svelte
<!-- ✅ -->
<Button on:click={pull} icon={faRotateRight}>Refresh</Button>
<ErrorMessage error={error} />

<!-- ❌ -->
<button class="px-4 py-2 bg-purple-600 text-white rounded" onclick={pull}>Refresh</button>
<div class="text-red-500">{error}</div>
```
