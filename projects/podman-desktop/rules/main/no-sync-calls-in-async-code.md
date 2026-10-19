---
title: Don't call synchronous `fs` or `child_process` APIs in async code; await the promise-based API
severity: warning
applies_to: ["packages/main/src/**/*.ts", "extensions/**/src/**/*.ts"]
exclude: ["**/*.spec.ts", "**/tests/**"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/19147#pullrequestreview-5144336121
  - https://github.com/podman-desktop/podman-desktop/pull/17589#discussion_r3381614736
  - https://github.com/podman-desktop/podman-desktop/pull/18140#discussion_r3506326189
---
## Check
Does an added or modified line call a synchronous Node.js `fs` or `child_process` API (a `…Sync` function) inside an `async` function, a promise callback or a service method, or add an `eslint-disable` for `n/no-sync`?

## Violation
- `fs.readFileSync`, `fs.writeFileSync`, `readdirSync`, `statSync`, `mkdirSync`, `rmSync`, `copyFileSync`, `renameSync`, `unlinkSync`, `lstatSync`, `accessSync` from `node:fs`.
- `execSync`, `execFileSync`, `spawnSync` from `node:child_process`.
- An added `// eslint-disable-next-line n/no-sync` (or `eslint-disable n/no-sync`) to hide one: `n/no-sync` is only a warning in `eslint.config.mjs`, so it's easy to ignore.

## Not a violation
- `existsSync` (ignored by the `n/no-sync` config).
- The async APIs: `readFile`, `writeFile`, `mkdir`, `stat`, `rm` from `node:fs/promises`, `fs.promises.*`, `exec` from `/@/plugin/util/exec.js` or the extension API `process.exec`.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
async function writeReport(target: string, text: string): Promise<void> {
  // eslint-disable-next-line n/no-sync
  fs.writeFileSync(target, text, 'utf8');
}

// ✅ Correct
import { writeFile } from 'node:fs/promises';

async function writeReport(target: string, text: string): Promise<void> {
  await writeFile(target, text, 'utf8');
}
```
