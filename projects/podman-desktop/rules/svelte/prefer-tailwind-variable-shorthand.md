---
title: Use Tailwind 4's text-(--pd-x) shorthand, not text-[var(--pd-x)]
severity: warning
applies_to: ["packages/renderer/src/**/*.svelte", "packages/ui/src/**/*.svelte"]
---
## Check
Does an added or modified class use the arbitrary-value form `…-[var(--…)]` for a CSS variable?

## Violation
`text-[var(--pd-content-text)]`, `bg-[var(--pd-content-card-bg)]`: Tailwind 4 writes them `text-(--pd-content-text)`.

## Not a violation
- A class the line already had before the change: a moved or re-indented line, or an edit that changes another class (`rounded-md` → `{rounded}`) and keeps `bg-[var(--pd-content-card-bg)]`. Don't rewrite existing classes in the same PR.
- Arbitrary values that aren't a lone variable (`w-[calc(100%-2rem)]`).
- Lines that were not added or modified by the change.

## Examples
```svelte
<!-- ❌ --> <span class="text-[var(--pd-content-text)]">
<!-- ✅ --> <span class="text-(--pd-content-text)">
```
