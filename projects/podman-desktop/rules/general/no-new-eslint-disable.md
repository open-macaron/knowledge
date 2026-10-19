---
title: Fix the code instead of adding an `eslint-disable` comment
severity: warning
applies_to: ["**/*.ts", "**/*.svelte", "**/*.js", "**/*.mjs", "**/*.cjs"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/18140#discussion_r3506665619
  - https://github.com/podman-desktop/podman-desktop/pull/17927#discussion_r3453587972
  - https://github.com/podman-desktop/podman-desktop/pull/15671#discussion_r2685645901
---
## Check
Does the change add a new `eslint-disable`, `eslint-disable-next-line` or `eslint-disable-line` comment (in `//`, `/* */` or `<!-- -->` form)?

## Violation
An added line such as `/* eslint-disable @typescript-eslint/no-explicit-any */`, `// eslint-disable-next-line n/no-sync`, `// eslint-disable-next-line sonarjs/…` or `<!-- eslint-disable-next-line svelte/no-at-html-tags -->` that wasn't there before, in tests and production code alike.

## Not a violation
- An existing disable comment that the change only moves, reindents or keeps next to code it edits (the same rule already appeared in a removed line).
- Removing a disable comment.
- Changes to the rule configuration in `eslint.config.mjs` (judged on their own merit, not by this rule).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
/* eslint-disable @typescript-eslint/no-explicit-any */
const extension = { id: 'x' } as any;

// ✅ Correct
const extension: Partial<Extension> = { id: 'x' };
```
