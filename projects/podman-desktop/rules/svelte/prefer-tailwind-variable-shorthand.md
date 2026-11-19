---
title: Use Tailwind 4's text-(--pd-x) shorthand, not text-[var(--pd-x)]
severity: warning
applies_to: ["packages/renderer/src/**/*.svelte", "packages/ui/src/**/*.svelte"]
sources:
  - https://github.com/podman-desktop/podman-desktop/blob/43ab21a323e014a4f4429e219adc5ae071d7e39b/.agents/skills/review-podman-desktop/references/rules.md#L44
---
## Check
Does an added or modified class use the arbitrary-value form `…-[var(--…)]` for a CSS variable?

## Violation
`text-[var(--pd-content-text)]`, `bg-[var(--pd-content-card-bg)]`: Tailwind 4 writes them `text-(--pd-content-text)`.

## Not a violation
- Existing classes on lines the change doesn't otherwise touch: don't rewrite them in the same PR.
- Arbitrary values that aren't a lone variable (`w-[calc(100%-2rem)]`).
- Lines that were not added or modified by the change.

## Examples
```svelte
<!-- ❌ --> <span class="text-[var(--pd-content-text)]">
<!-- ✅ --> <span class="text-(--pd-content-text)">
```
