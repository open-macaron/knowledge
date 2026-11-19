---
title: Take Podman Desktop's own paths from the injected Directories
severity: warning
applies_to: ["packages/main/src/**/*.ts"]
exclude: ["**/*.spec.ts", "packages/main/src/plugin/directories*.ts"]
---
## Check
Does an added or modified line build a path to Podman Desktop's own configuration, data, plugins, extensions storage or contributions from `os.homedir()`, `app.getPath(…)` or an environment variable, instead of a `Directories` method?

## Violation
`path.join(homedir(), '.local/share/containers/podman-desktop', …)`: Podman Desktop has two layouts (legacy and Linux XDG), and only `Directories` (`packages/main/src/plugin/directories.ts`) knows which one applies.

## Not a violation
- `this.directories.getConfigurationDirectory()` and the other `Directories` methods.
- Paths of other tools: `~/.kube/config`, `~/.redhat`, the Podman install directory.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌
const file = join(homedir(), '.local/share/containers/podman-desktop', 'feature.json');
// ✅
constructor(@inject(Directories) private readonly directories: Directories) {}
const file = join(this.directories.getConfigurationDirectory(), 'feature.json');
```
