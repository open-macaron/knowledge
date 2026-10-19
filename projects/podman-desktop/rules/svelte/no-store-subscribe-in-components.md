---
title: Read Svelte stores with $store and $derived in components, not .subscribe() copying into local state
severity: warning
applies_to: ["**/*.svelte"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/15112#discussion_r2571806022
  - https://github.com/podman-desktop/podman-desktop/pull/15671#discussion_r2685632863
  - https://github.com/podman-desktop/podman-desktop/pull/19048#discussion_r3911856855
  - https://svelte.dev/docs/svelte/stores#Using-stores
---
## Check
Does an added or modified line in a component call `.subscribe(…)` on a store only to copy (or map) its value into a local variable, where `$store` inside `$derived(…)` would do?

## Violation
- `someStore.subscribe(value => { local = value; })` or `local = value.map(…)` / `.filter(…)`, typically inside `onMount` with an `Unsubscriber` released in `onDestroy` or returned from `onMount`.
- `get(someStore)` from `svelte/store` read once into a `$state` that is meant to follow the store.

## Not a violation
- Subscriptions whose callback does side effects (calls `window.*`, navigates, starts a timer, updates selection while keeping user state) rather than only deriving a value.
- `.subscribe` in `.ts` store modules (`packages/renderer/src/stores/*.ts`), and `router.subscribe` in `App.svelte`.
- Reading `$filtered`, `$providerInfos` and the like directly, or in `$derived(…)`.
- Lines that were not added or modified by the change.

## Examples
```svelte
<script lang="ts">
// ❌ Incorrect
let volumes = $state<VolumeInfoUI[]>([]);
let volumesUnsubscribe: Unsubscriber;
onMount(() => {
  volumesUnsubscribe = filtered.subscribe(value => {
    volumes = value.filter(volume => volume.engineId === selectedEnvironment);
  });
});
onDestroy(() => volumesUnsubscribe?.());

// ✅ Correct
let volumes = $derived($filtered.filter(volume => volume.engineId === selectedEnvironment));
</script>
```
