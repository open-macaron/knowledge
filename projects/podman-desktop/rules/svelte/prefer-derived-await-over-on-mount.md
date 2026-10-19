---
title: Load a value once with $derived(await …), not with onMount filling a $state or a Promise in $derived
severity: warning
applies_to: ["packages/renderer/src/**/*.svelte"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/15536#discussion_r2661609277
  - https://github.com/podman-desktop/podman-desktop/pull/15605#discussion_r2675268179
  - https://github.com/podman-desktop/podman-desktop/pull/18787#pullrequestreview-4972302225
  - https://github.com/podman-desktop/podman-desktop/blob/main/CODE-GUIDELINES.md#using-asyncawait-in-expressions
  - https://svelte.dev/docs/svelte/await-expressions
---
## Check
Does an added or modified line load a value with `onMount(async () => { x = await … })` into a `$state`, or store an un-awaited Promise in `$derived(…)`, where `let x = $derived(await …)` would do?

## Violation
- `let x = $state(…)` followed by `onMount(async () => { x = await window.someCall(); })` (or `.then(v => x = v)`) whose only job is to fill that state.
- `let x = $derived(window.someCall())` (a Promise kept in a derived) read through `{#await x}` just to get the value, with no loading or error UI.
Experimental async is enabled (`compilerOptions.experimental.async` in `svelte.config.js`), and main already uses `$derived(await window.getOsPlatform())` (`NavigationButtons.svelte`), `$derived(await window.helpMenuGetItems())` (`HelpActions.svelte`).

## Not a violation
- A call that is slow or can fail and needs a visible loading state or error UI: `{#await promise}…{:catch error}` or a `<svelte:boundary>` with `pending`/`failed` snippets (e.g. `BuildTargetDropdown.svelte`, whose Promise depends on a prop).
- `onMount` that registers listeners, subscriptions or timers, or does other side effects besides filling the state.
- Lines that were not added or modified by the change.

## Examples
```svelte
<script lang="ts">
// ❌ Incorrect
let isMac = $state(false);
onMount(async () => {
  isMac = (await window.getOsPlatform()) === 'darwin';
});
let images = $derived(window.listImages()); // a Promise

// ✅ Correct
let isMac = $derived((await window.getOsPlatform()) === 'darwin');
let images = $derived(await window.listImages());
</script>
```
