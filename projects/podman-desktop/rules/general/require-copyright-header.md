---
title: Start every new `.ts` file and workflow with the Red Hat Apache-2.0 header and the current year
severity: warning
applies_to: ["**/*.ts", ".github/workflows/*.yaml", ".github/workflows/*.yml"]
sources:
  - https://github.com/podman-desktop/podman-desktop/pull/14689#discussion_r2480649802
  - https://github.com/podman-desktop/podman-desktop/pull/15671#discussion_r2685624471
  - https://github.com/podman-desktop/podman-desktop/pull/16637#discussion_r2940052921
  - https://github.com/podman-desktop/podman-desktop/pull/17816#discussion_r3388384338
---
## Check
Does an added file lack the Red Hat Apache-2.0 header with the current year at the time of the change (read the commit date with `git log -1 --format=%cd`), or does a modified header lose its start year?

## Violation
- A new `.ts` file whose first lines aren't the block `/****…` / ` * Copyright (C) <year> Red Hat, Inc.` / the Apache License, Version 2.0 text / ` * SPDX-License-Identifier: Apache-2.0` / ` ****…/`.
- A new `.github/workflows/*.yaml` whose first lines aren't the same text as `#` comments: `#`, `# Copyright (C) <year> Red Hat, Inc.`, …, `# SPDX-License-Identifier: Apache-2.0`.
- A new file whose year isn't the current year, e.g. a header copied with `2022-2025` from another file.
- An edited header whose start year is replaced (`2024` → `2026`) instead of becoming a range (`2024-2026`).

## Not a violation
- `.svelte` files, which have no header.
- An existing file without a header, or with an older year, that the change doesn't touch at the top.
- An edited file that keeps its header year unchanged.
- Lines that were not added or modified by the change.

## Examples
```ts
// ❌ Incorrect: a new file without the header
import { readFile } from 'node:fs/promises';

// ❌ Incorrect: an edited header losing its start year
- * Copyright (C) 2024 Red Hat, Inc.
+ * Copyright (C) 2026 Red Hat, Inc.

// ✅ Correct: a new file created in 2026
/**********************************************************************
 * Copyright (C) 2026 Red Hat, Inc.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * …
 * SPDX-License-Identifier: Apache-2.0
 ***********************************************************************/

// ✅ Correct: an edited header
 * Copyright (C) 2024-2026 Red Hat, Inc.
```
