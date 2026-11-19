---
title: Style components with Tailwind utilities, not <style> blocks or style attributes
severity: warning
applies_to: ["packages/renderer/src/**/*.svelte", "packages/ui/src/**/*.svelte"]
---
## Check
Does the change add a `<style>` block, or a static `style="…"` attribute, to a component?

## Violation
`<style>.title { color: red; }</style>` or `<div style="margin-top: 8px">`: use utility classes, and add an `@utility` when a value is missing.

## Not a violation
- A `style` whose value is computed at runtime: `style="width: {progress}%"`, `style:width`.
- `fill` / `stroke` on SVG icon elements.
- Lines that were not added or modified by the change.

## Examples
```svelte
<!-- ❌ --> <div style="margin-top: 8px">
<!-- ✅ --> <div class="mt-2">
```
