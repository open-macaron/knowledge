---
title: Get services through `@inject()` constructor parameters, not `new`, statics or manual `init()` calls
severity: warning
applies_to: ["packages/main/src/**/*.ts", "extensions/podman/packages/extension/src/**/*.ts"]
exclude: ["**/*.spec.ts"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/13981#discussion_r2445059494
  - https://github.com/podman-desktop/podman-desktop/pull/14697#discussion_r2481252461
  - https://github.com/podman-desktop/podman-desktop/pull/14980#issuecomment-3553752743
  - https://github.com/podman-desktop/podman-desktop/pull/15609#discussion_r2685658099
---
## Check
Does an added or modified line create or reach an `@injectable()` service by hand (`new`, a static method, a module singleton) or add a manual `.init()` call that `@postConstruct()` should run?

## Violation
- `new DefaultConfiguration()`, `new ContainerfileParser(ipcHandle)` or `new` of any other class decorated `@injectable()`, outside the container setup.
- A static method or static field on a service used as a shortcut instead of an injected instance.
- An added `container.get<NavigationManager>(NavigationManager).init()` or `service.init()` for a new service whose `init()` only needs its injected dependencies.

## Not a violation
- `container.bind<X>(X).toSelf().inSingletonScope()`, `toConstantValue(…)` and `container.get(…)` in the container setup (`packages/main/src/plugin/index.ts`, `main.ts`).
- `new` of value objects and library types: `new Map()`, `new Disposable(…)`, `new Error(…)`, `new URL(…)`, `new Emitter()`.
- Existing manual `init()` calls that the change only moves or keeps.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect
const defaultConfiguration = new DefaultConfiguration(directories);
const info = await new ContainerfileParser(ipcHandle).parseContainerFile(path);
container.get<NavigationManager>(NavigationManager).init();

// ✅ Correct
import { inject, injectable, postConstruct } from 'inversify';

@injectable()
export class ImageBuilder {
  constructor(
    @inject(DefaultConfiguration) private readonly defaultConfiguration: DefaultConfiguration,
    @inject(ContainerfileParser) private readonly containerfileParser: ContainerfileParser,
  ) {}

  @postConstruct()
  init(): void { … }
}
```
