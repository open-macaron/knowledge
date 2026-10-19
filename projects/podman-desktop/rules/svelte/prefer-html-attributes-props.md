---
title: Extend HTMLAttributes and spread the rest props on the root element instead of class/style/xxxClass props
severity: warning
applies_to: ["packages/renderer/src/**/*.svelte", "packages/ui/src/**/*.svelte"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/17709#discussion_r3419334629
  - https://github.com/podman-desktop/podman-desktop/pull/18724#discussion_r3861447609
  - https://github.com/podman-desktop/podman-desktop/pull/18689#discussion_r3794837767
  - https://github.com/podman-desktop/podman-desktop/pull/18614#discussion_r3727976087
  - https://svelte.dev/docs/svelte/typescript#Typing-wrapper-components
---
## Check
Does an added or modified `Props` interface of a component with a single root element declare its own `class`, `style` or `xxxClass` string props instead of extending `HTMLAttributes<…>` from `svelte/elements` and spreading the rest?

## Violation
- `interface Props { …; class?: string }` or `style?: string` declared by hand, without `extends HTMLAttributes<HTMLDivElement>` (or the root element's type, `HTMLButtonAttributes`, `HTMLInputAttributes`…).
- New `containerClass?: string`, `wrapperClass?: string`, `labelClass?: string` props added to style the root element.
- A component that extends `HTMLAttributes` but destructures without `...restProps`, or never spreads `{...restProps}` on its root element.

## Not a violation
- `class: className` destructured from props that extend `HTMLAttributes`, then merged on the root: `class={['flex h-full', className]}` (as in `OnboardingWizardShell.svelte`, `ButtonRow.svelte`).
- Components without a single root element (fragments, snippet-only wrappers), or a `xxxClass` prop for an inner, non-root element when the component already extends `HTMLAttributes`.
- Lines that were not added or modified by the change.

## Examples
```svelte
<script lang="ts">
// ❌ Incorrect
interface Props {
  title: string;
  class?: string;
  containerClass?: string;
}
let { title, class: className, containerClass }: Props = $props();

// ✅ Correct
import type { HTMLAttributes } from 'svelte/elements';
interface Props extends HTMLAttributes<HTMLDivElement> {
  title: string;
}
let { title, class: className, ...restProps }: Props = $props();
</script>
<div class={['flex flex-col', className]} {...restProps}>{title}</div>
```
