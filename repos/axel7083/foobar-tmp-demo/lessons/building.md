---
topic: Building
description: How to build foobar-tmp-demo and what the build proves (types). Use before building, testing or changing the build.
sources:
  - https://github.com/axel7083/foobar-tmp-demo/pull/2#discussion_r4285183794
last_verified: 2026-10-15
confidence: high
---
`npm run build` is `tsc`: it type-checks and emits in one step, so a passing build means the types are right. There are no tests to run.
