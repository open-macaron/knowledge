---
title: Use Svelte 5 runes, not legacy reactivity
severity: error
applies_to: ["**/*.svelte"]
sources:
  - https://svelte.dev/docs/svelte/v5-migration-guide
---
## Check
Does the changed code declare props or reactive state with Svelte 4 syntax instead of runes?

## Violation
An added or modified line that uses `export let` for a prop, a `$:` reactive statement or declaration, or `createEventDispatcher` to emit events.

## Not a violation
- `let { name } = $props();`, `$state(…)`, `$derived(…)`, `$derived.by(…)`, `$effect(…)`, `$bindable()`.
- `$store` auto-subscriptions to Svelte stores (a `$` prefix on a store variable, not a `$:` label).
- `on:click` and other `on:` events on `@podman-desktop/ui-svelte` components such as `Button`: that is how those components emit events.
- Lines that were not added or modified by the change.

## Examples
```svelte
<script lang="ts">
// ✅
interface Props { path: string; loading?: boolean }
let { path, loading = $bindable() }: Props = $props();
let language = $derived(path.split('.').at(-1));

// ❌
export let path: string;
$: language = path.split('.').at(-1);
</script>
```
