---
title: Log the error with context in a `catch` that doesn't rethrow
severity: warning
applies_to: ["**/*.ts", "**/*.svelte"]
exclude: ["**/*.spec.ts", "**/tests/**", "**/__mocks__/**"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/18521#discussion_r3727998592
  - https://github.com/podman-desktop/podman-desktop/pull/18337#discussion_r3813953445
  - https://github.com/podman-desktop/podman-desktop/pull/19043#discussion_r4103974356
---
## Check
Does an added or modified `catch` block (or `.catch(…)` handler) swallow the error, neither rethrowing it nor logging it with `console.error` / `console.warn`?

## Violation
- `catch {}` or `catch { return ''; }` / `catch { return undefined; }` with no log.
- `catch (error) { }` or a block that ignores `error`.
- `.catch(() => {})`, `.catch(() => undefined)`, `.catch(() => [])`.

## Not a violation
- A `catch` that rethrows (`throw error`, `throw new Error(…, { cause: error })`) or rejects.
- A `catch` that logs with `console.error` / `console.warn` and a message saying what failed, then falls back.
- A `catch` that reports the error to the user or caller instead (sets an error state shown in the UI, `window.showMessageBox`, returns it in a result object, `telemetry` with the error).
- An expected failure checked explicitly, with a comment saying why it's ignored (e.g. a file that may not exist, `ENOENT`).
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
try {
  return await readFile(readmePath, 'utf8');
} catch {
  return '';
}

// ✅ Correct
try {
  return await readFile(readmePath, 'utf8');
} catch (error: unknown) {
  console.error(`cannot read the README of ${extensionId}`, error);
  return '';
}
```
