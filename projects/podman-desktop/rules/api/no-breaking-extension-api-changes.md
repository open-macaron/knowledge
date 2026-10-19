---
title: Keep extension API changes additive, optional and documented; never rename, remove or retype members
severity: error
applies_to: ["packages/extension-api/src/extension-api.d.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/15233#discussion_r2597594740
  - https://github.com/podman-desktop/podman-desktop/pull/15569#discussion_r2737496995
  - https://github.com/podman-desktop/podman-desktop/pull/16539#discussion_r2932165749
  - https://github.com/podman-desktop/podman-desktop/pull/18215#discussion_r3547508647
---
## Check
Does an added or modified line in `extension-api.d.ts` break existing extensions (compare it with the removed lines of the same hunk in `git diff`), or add API that is not optional and documented?

## Violation
An added line that replaces a removed one and:
- renames or removes an exported function, interface, field, enum member or namespace member (`Id` → `id`);
- changes a parameter list, a return type (`Disposable` → `Promise<Disposable>`), a field type or an `Event<T>` payload type;
- adds a required field to a type that extensions construct or implement (`*Options`, `Provider*`, `*Connection`, `Registry`, callbacks);
- adds a new positional parameter to an existing function instead of a new optional field in its `*Options` object.

Also, an added declaration that:
- has a new `options: XxxOptions` parameter that is required although all fields of `XxxOptions` are optional (write `options?: XxxOptions`);
- adds a new exported member or field without a `/** … */` doc comment.

## Not a violation
- New optional fields (`foo?: T`) with a doc comment, new functions, new interfaces, new optional parameters at the end of a *new* function.
- Widening a parameter type or adding a union member to an input-only type; doc-comment-only edits.
- Fields added to result types that only Podman Desktop constructs (`NetworkCreateResult`), as long as existing fields keep their name and type.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
export interface NetworkCreateResult { id: string }                        // was `Id: string`
export function registerRegistry(registry: Registry): Promise<Disposable>; // was `Disposable`
export function buildImage(context: string, eventCollect: BuildCallback, options?: BuildImageOptions, validateRegistries?: boolean): Promise<unknown>;
export function listContexts(options: ListContextsOptions): Promise<Context[]>; // new, all fields of ListContextsOptions optional

// ✅ Correct
export interface NetworkCreateResult { Id: string; engineId: string }
export interface BuildImageOptions {
  /** Check the registries' credentials before building. */
  validateRegistries?: boolean;
}
export function listContexts(options?: ListContextsOptions): Promise<Context[]>;
```
