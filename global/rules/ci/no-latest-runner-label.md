---
title: Name the runner image instead of a `-latest` label
severity: error
applies_to: [".github/workflows/**"]
sources:
  - https://github.com/podman-desktop/agent-skills/pull/15#discussion_r4585449409
  - https://github.blog/changelog/2026-09-17-ubuntu-26-generally-available-and-latest-migration/
---
## Check
Does an added or modified line choose a GitHub-hosted runner with a `-latest` label (`ubuntu-latest`, `ubuntu-latest-arm`, `windows-latest`, `macos-latest`, …), in `runs-on:` or in a matrix value that `runs-on:` uses?

## Violation
An added or modified line such as `runs-on: ubuntu-latest`, `runs-on: [windows-latest]` or `os: [ubuntu-latest, macos-latest, windows-latest]`: GitHub moves a `-latest` label to the next image on its own schedule, so the job's OS and preinstalled tools change without a commit.

## Not a violation
- A versioned label: `ubuntu-26.04`, `ubuntu-24.04-arm`, `windows-2025`, `macos-15`.
- Self-hosted or larger-runner labels the repository defines (`self-hosted`, `linux-x64-16core`).
- `runs-on: ${{ matrix.os }}` itself: the matrix values are what is checked.
- Lines that were not added or modified by the change.

## Examples
```yaml
# ❌ Incorrect
runs-on: ubuntu-latest
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]

# ✅ Correct
runs-on: ubuntu-26.04
strategy:
  matrix:
    os: [ubuntu-26.04, windows-2025]
```
