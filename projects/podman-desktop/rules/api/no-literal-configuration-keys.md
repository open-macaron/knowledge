---
title: Build configuration keys from a *Settings enum of packages/api, not string literals
severity: warning
applies_to: ["packages/main/src/**/*.ts", "packages/renderer/src/**/*.ts", "packages/renderer/src/**/*.svelte"]
exclude: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/15112#discussion_r2571069627
  - https://github.com/podman-desktop/podman-desktop/pull/15506#discussion_r2668956580
  - https://github.com/podman-desktop/podman-desktop/pull/16066#discussion_r2767998734
  - https://github.com/podman-desktop/podman-desktop/pull/16425#discussion_r2906643773
---
## Check
Does an added or modified line pass a configuration key or section as a string literal instead of building it from a `*Settings` enum exported by `packages/api` (`@desktop-framework/api`)?

## Violation
- A literal key in `window.getConfigurationValue<T>('…')`, `window.updateConfigurationValue('…', …)`, `window.isExperimentalConfigurationEnabled('…')`, `window.updateExperimentalConfigurationValue('…', …)`.
- In main: `configurationRegistry.getConfiguration('preferences')`, `.get<T>('update.appUpdate')`, `.update('…', …)`, or a literal property key in `registerConfigurations([{ properties: { 'dockerCompatibility.enabled': … } }])`.
- A new configuration whose section/key names are not added to a `*Settings` enum in `packages/api/src` (like `DockerCompatibilitySettings`, `AppearanceSettings`, `TelemetrySettings`, `ExtensionLoaderSettings`).

## Not a violation
- Keys built from the enum: `` `${DockerCompatibilitySettings.SectionName}.${DockerCompatibilitySettings.Enabled}` `` or `AppearanceSettings.SectionName + '.' + AppearanceSettings.Appearance`.
- Keys that come from data (an extension's contributed configuration, a loop over registered properties).
- Specs, and extensions under `extensions/**` (they own their keys in their `package.json`).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
await window.getConfigurationValue<boolean>('dockerCompatibility.enabled');
await window.isExperimentalConfigurationEnabled('dashboard.enhancedDashboard');

// ✅ Correct
import { DockerCompatibilitySettings } from '@desktop-framework/api';
await window.getConfigurationValue<boolean>(
  `${DockerCompatibilitySettings.SectionName}.${DockerCompatibilitySettings.Enabled}`,
);
```
