---
title: Register theme colors with tailwind-color-palette.json values, never hex or rgba literals
severity: error
applies_to: ["packages/main/src/plugin/color-registry.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/16842#issuecomment-4136469693
  - https://github.com/podman-desktop/podman-desktop/pull/16825#discussion_r2996031207
  - https://github.com/podman-desktop/podman-desktop/pull/16749#discussion_r2974595399
  - https://github.com/podman-desktop/podman-desktop/pull/14819#discussion_r2522455595
---
## Check
Does an added or modified line in `color-registry.ts` give a color as a hex (`'#7E60E6'`), `rgb(…)`/`rgba(…)` or named CSS literal instead of a value from `tailwind-color-palette.json`?

## Violation
- A string color literal in `registerColor(…, { dark, light, hcDark, hcLight })`, in `.withLight(…)`/`.withDark(…)`/`.withHcLight(…)`/`.withHcDark(…)`, or passed to `colorPaletteHelper('#…')`.
- A new color constant declared in the file (`const brand = '#8F8F8F'`) instead of adding the shade to `tailwind-color-palette.json`.

## Not a violation
- Palette values destructured from `tailwindColorPalette`: `purple[500]`, `charcoal[900]`, `accent1[400]`, `gray[50]`, `black`, `white`, `transparent`.
- Transparency through the helper: `colorPaletteHelper(accent1[600]).withAlpha(0.1)`.
- Hex colors inside the JSDoc examples of the file, and existing literals (`'#202020'`, `'#c42b1c'` for Windows title bar) that the change does not touch.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
this.registerColor(`${status}running`, { dark: '#7E60E6', light: '#8F8F8F', hcDark: '#FFFFFF', hcLight: '#000000' });
.withDark(colorPaletteHelper('#000000').withAlpha(0.8))

// ✅ Correct
this.registerColor(`${status}running`, { dark: purple[500], light: gray[500], hcDark: white, hcLight: black });
.withDark(colorPaletteHelper(charcoal[900]).withAlpha(0.8))
```
