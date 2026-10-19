---
title: Use `undefined` for an absent value, not `null` or an empty string
severity: warning
applies_to: ["**/*.ts", "**/*.svelte"]
exclude: ["**/*.spec.ts", "**/tests/**", "**/__mocks__/**"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/16499#discussion_r2905764017
  - https://github.com/podman-desktop/podman-desktop/pull/17550#pullrequestreview-4321099272
  - https://github.com/podman-desktop/podman-desktop/pull/17631#discussion_r3316697449
  - https://github.com/podman-desktop/podman-desktop/pull/19317#discussion_r4045682706
---
## Check
Does an added or modified line represent "no value" with `null` or `''` in a type, initializer, assignment or return that the change itself defines?

## Violation
- A type with `| null` declared by the change: `let savedRoute: string | null = null;`, `error?: string | null`, `Promise<Foo | null>`.
- Assigning or returning `null` as "nothing": `return null;`, `this.current = null;`.
- An empty string used as the absent marker: `let volumeNameError = '';` later tested with `if (volumeNameError)`.

`eslint-plugin-no-null` is registered in `eslint.config.mjs` but its rule isn't enabled, so lint won't catch it.

## Not a violation
- Comparing with or handling `null` returned by DOM, Node.js or library APIs (`document.querySelector`, `match()`, `localStorage.getItem`, dockerode or Kubernetes client fields typed `| null`).
- `JSON.stringify(value, null, 2)`, and `null` required by an external API or wire format.
- An empty string that is a real value (an empty input, a default text).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
let savedRoute: string | null = null;
let volumeNameError = ''; // '' means "no error"

// ✅ Correct
let savedRoute: string | undefined;
let volumeNameError = $state<string>();
```
