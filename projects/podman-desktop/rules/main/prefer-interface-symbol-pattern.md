---
title: Name an Inversify interface symbol after its interface and bind classes as singletons
severity: warning
applies_to: ["packages/main/src/**/*.ts", "packages/api/src/**/*.ts"]
exclude: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/blob/43ab21a323e014a4f4429e219adc5ae071d7e39b/.agents/skills/review-podman-desktop/references/rules.md#L79
---
## Check
Does an added injection symbol or binding break the pattern: `export const Foo = Symbol.for('Foo')` declared right before `export interface Foo` (same name), classes bound with `bind(X).toSelf().inSingletonScope()`?

## Violation
- A `FooSymbol` / `FOO_TOKEN` name, a symbol separate from its interface, or `Symbol('Foo')` instead of `Symbol.for`. The same name lets TypeScript merge the value and the type, so `@inject(Foo) foo: Foo` reads naturally.
- A class bound without `.inSingletonScope()`.

## Not a violation
- `container.bind(Foo).toService(FooImpl)` or `.to(Impl)` to map an interface symbol.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌
export const DirectoriesSymbol = Symbol('Directories');
// ✅ (packages/main/src/plugin/directories.ts)
export const Directories = Symbol.for('Directories');
export interface Directories { … }
container.bind<ColorRegistry>(ColorRegistry).toSelf().inSingletonScope();
```
