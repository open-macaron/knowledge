---
title: Write Svelte 5 runes and callbacks, not Svelte 4 syntax
severity: warning
applies_to: ["packages/renderer/src/**/*.svelte"]
---
## Check
Does an added or modified line use Svelte 4 syntax: `$:`, `export let`, an `on:event` directive, `createEventDispatcher` or `<slot>`?

## Violation
Any of them: runes mode isn't forced in `svelte.config.js`, so legacy syntax still compiles and only review catches it.

## Not a violation
- `$derived`, `$props()`, `onclick={…}`, callback props, snippets (`{@render children?.()}`).
- `packages/ui`, which keeps `on:click` and dispatch for Svelte 4 extension callers (not in scope).
- Syntax the line already had before the change: a moved line, or an edit that keeps an existing `on:click` while changing another attribute.
- Lines that were not added or modified by the change.

## Examples
```svelte
<!-- ❌ -->
export let name: string;
$: label = name.toUpperCase();
<Button on:click={save}>Save</Button>
<!-- ✅ -->
let { name }: Props = $props();
const label = $derived(name.toUpperCase());
<Button onclick={save}>Save</Button>
```
