---
title: Compute values from other state with $derived, not by assigning $state inside an $effect
severity: warning
applies_to: ["**/*.svelte", "**/*.svelte.ts"]
exclude: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/17977#pullrequestreview-4534008640
  - https://github.com/podman-desktop/podman-desktop/pull/18090#discussion_r3490032643
  - https://github.com/podman-desktop/podman-desktop/pull/18662#discussion_r3978246414
  - https://github.com/podman-desktop/podman-desktop/pull/14459#discussion_r2490852608
  - https://svelte.dev/docs/svelte/$effect#When-not-to-use-$effect
---
## Check
Does an added or modified line compute a value from props, state or stores by assigning a `$state` variable inside `$effect(…)`, or with a plain `const`/`let` that won't update when its inputs change?

## Violation
- An `$effect(() => { x = <expression of other state/props/$store>; })` whose body only assigns state from a synchronous expression (`.find`, `.filter`, `.map`, `.some`, arithmetic, ternaries, string templates).
- A top-level `const hasX = props.foo.some(…)` (or `let x = props.foo.bar`) in a component script that reads props or state without `$derived`, so the value goes stale when the prop changes.

## Not a violation
- Effects that touch the DOM, call IPC (`window.*`), start timers or subscriptions, write to a store (`searchPattern.set(searchTerm)`), or return a cleanup function.
- Effects that assign state from an awaited call (that's asynchronous work, see `svelte/prefer-derived-await-over-on-mount`).
- `$derived(…)` / `$derived.by(…)`, and constants computed from non-reactive values (imports, literals).
- Lines that were not added or modified by the change.

## Examples
```svelte
<script lang="ts">
// ❌ Incorrect
let network = $state<NetworkInfoUI>();
$effect(() => {
  network = $networksListInfo.find(n => n.name === networkName && n.engineId === engineId);
});
const hasPaused = pod.containers.some(c => c.Status === 'paused'); // stale when `pod` changes

// ✅ Correct
let network = $derived($networksListInfo.find(n => n.name === networkName && n.engineId === engineId));
const hasPaused = $derived(pod.containers.some(c => c.Status === 'paused'));
</script>
```
