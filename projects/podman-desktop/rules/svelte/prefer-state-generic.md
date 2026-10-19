---
title: Type $state with its generic, $state<T>(), instead of annotating the variable
severity: warning
applies_to: ["**/*.svelte", "**/*.svelte.ts"]
exclude: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/18751#discussion_r3797272661
  - https://github.com/podman-desktop/podman-desktop/pull/18648#discussion_r3843332246
  - https://github.com/podman-desktop/podman-desktop/pull/18662#discussion_r3820607853
---
## Check
Does an added or modified line declare a `$state` (or `$state.raw`) with a type annotation on the variable, `let x: T = $state(…)`, instead of `let x = $state<T>(…)`?

## Violation
- `let x: T | undefined = $state();` or `= $state(undefined);` (write `$state<T>()`: the generic already adds `| undefined` when there is no initial value).
- `let items: Item[] = $state([]);`, `let map: Map<string, X> = $state(new Map());`, `let x: T = $state.raw(…)`.

## Not a violation
- `let x = $state('')`, `$state(false)`, `$state(0)`: the type is inferred from the initial value.
- `let x = $state<T>()`, `$state<T[]>([])`, `$state<T | undefined>(initial)`.
- Annotations on `$props()` destructuring (`let { a }: Props = $props()`) and on `$derived`.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
let detailsPage: DetailsPage | undefined = $state(undefined);
let refreshTimeouts: ReturnType<typeof setTimeout>[] = $state([]);

// ✅ Correct
let detailsPage = $state<DetailsPage>();
let refreshTimeouts = $state<ReturnType<typeof setTimeout>[]>([]);
```
