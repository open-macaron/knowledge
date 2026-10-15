# open-macaron/knowledge

What [macaron-bot](https://github.com/open-macaron/macaron) knows about the projects it contributes to, reviewed by humans:

- **Rules:** what changed code must look like. Written by people. The bot follows them when it writes code and checks them, one by one, when it reviews.
- **Lessons:** facts learned while working ("e2e tests need `xvfb-run`", "reviewers prefer small PRs"). Proposed by the bot, approved by people.

Everything here is public, and so is what the bot enforces on each repository: see [Read API](#read-api).

## Layout

```
global/                       applies to every repository
├── OWNERS.yaml
├── rules/<slug>.md
└── lessons/<slug>.md
projects/<project>/           every repository of a project (same names as macaron's config/projects/)
├── OWNERS.yaml
├── rules/<slug>.md
└── lessons/<slug>.md
repos/<owner>/<repo>/         one upstream repository (its slug, not the bot's fork)
├── OWNERS.yaml
├── overview.md               layout, build and test quirks; given to the agent on every run
├── rules/<slug>.md
└── lessons/<slug>.md
```

- `<slug>` is kebab-case (`no-runtime-json-read`). A rule's id is `<scope>/<slug>`: `global/…`, `<project>/…` or `<owner>/<repo>/…`. Review tables cite it, so **an id is never reused** for another rule; delete a rule rather than repurpose it.
- A rule applies only where its folder applies: a rule in `projects/sandbox/` never reaches another project's repositories.
- Anything else (other files, other folders, symbolic links) fails the check. Files are at most 64 KiB.

## Rules

A rule is **one checkable thing**, written so that a small, cheap model can check it on its own against a diff: a narrow yes/no question, what counts and what doesn't, and examples.

````markdown
---
title: Import package.json instead of reading it at runtime
severity: error                  # error → ❌ violation in reviews, warning → ⚠️ warning
applies_to: ["src/**/*.ts"]      # optional globs (files or directories); omitted = every file
exclude: ["**/*.spec.ts"]        # optional
sources:                         # optional: where the rule comes from
  - https://github.com/axel7083/foobar-tmp-demo/pull/2#discussion_r4285183794
---
## Check
Does the changed code read `package.json` from disk at runtime?

## Violation
An added or modified line that reads `package.json` with `fs`.

## Not a violation
`import pkg from '../package.json'`; tests and build scripts.

## Examples
```ts
// ✅
import pkg from '../package.json';
// ❌
const pkg = JSON.parse(await readFile(join(root, 'package.json'), 'utf8'));
```
````

The four `##` sections are required, with these exact names, in this order. Globs are relative to the upstream repository's root and matched with [picomatch](https://github.com/micromatch/picomatch); they can't start with `/` or contain `..`.

### How rules are used

- **Selection:** only rules whose `applies_to` / `exclude` match the files in play are loaded: the PR's diff when reviewing, the bot's own diff before it publishes. While implementing, the agent gets the list of rule titles and globs and reads the ones that match what it touches.
- **Review:** each matching rule is checked separately by a cheap model, given only that rule and the matching parts of the diff. A violation must quote a line that the diff really adds; otherwise it is discarded. The review ends with a table:

  | Rule | Severity | Result | Evidence |
  |---|---|---|---|
  | `sandbox/no-runtime-json-read` | error | ❌ violation | `src/extension.ts:12` |
  | `global/conventional-commits` | warning | ⚠️ warning | commit `8c694b4` |
  | `sandbox/escape-webview-html` | error | ✅ valid | |
  | `sandbox/i18n-strings` | warning | ➖ ignored | no matching file changed |

- **The bot checks its own changes** the same way before publishing them.

## Lessons

````markdown
---
topic: Building
applies_to: ["src/**"]           # optional
sources:                         # required: the evidence
  - https://github.com/axel7083/foobar-tmp-demo/pull/2
last_verified: 2026-10-15
confidence: high                 # low | medium | high
---
`npm run build` runs `tsc`: it type-checks and emits in one step.
````

The bot never changes this repository directly: it opens pull requests from its fork, and the folder's owners decide. Only people write or promote rules.

## Owners and approvals

Each folder has an `OWNERS.yaml`:

```yaml
owners:
  - login: axel7083     # shown and @mentioned
    id: 42176370        # what approvals are matched on
```

Find an account's id with `gh api users/<login> --jq .id`. **The id is what counts**: a login can be renamed and then registered by someone else. The check fails when a login no longer belongs to its id, so the file gets updated, but access never moves to whoever holds the old name.

**Owners don't need write access to this repository.** GitHub doesn't read `OWNERS.yaml`; our workflows do:

1. Anyone opens a pull request, from a fork (nobody pushes branches here).
2. `check` validates the files and annotates problems on the files. It is feedback only.
3. An owner of each folder the pull request touches reviews it on GitHub and approves.
4. Each review wakes up `merge`, which decides from scratch and merges when it can. It keeps one comment on the pull request saying what is still missing, and @mentions the owners it waits for.

`merge` merges a pull request only when **all** of these hold:

- every changed path (both sides of a rename) is inside `global/`, `projects/<project>/` or `repos/<owner>/<repo>/`, and that folder's `OWNERS.yaml` **already exists on `main`**;
- for each of those folders, an owner listed in its `OWNERS.yaml` **on `main`** (by id) approved **the latest commit** of the pull request, and no owner of that folder has a standing "changes requested";
- the author's own approval doesn't count;
- the files are valid, checked by the pinned `knowledge-cli`.

It then merges exactly the commit it checked (squash): if something was pushed in the meantime, GitHub refuses.

Consequences:

- **Pushing again after an approval** needs a new approval.
- **A pull request touching two projects** needs an owner of each.
- **Owners manage their own `OWNERS.yaml`:** a change to it is approved by the owners currently on `main`.
- **New folders, `.github/`, this README and other root files** are merged by the platform team by hand. That's also how a new project or repository gets its first `OWNERS.yaml`.

### Why it's safe

Approvals are checked by a workflow rather than GitHub's branch protection, so the workflows assume the worst about pull requests:

- For a pull request from a fork, GitHub runs **the pull request's own copy** of `check.yml` and `review.yml`. They get no write permission and no secret, and nothing trusts what they do: they only wake `merge` up. A pull request can even add a workflow with the same name; that changes nothing.
- `merge.yml` always runs **from `main`**. From the event that woke it up it uses only the commit SHA, to find which open pull request to look at, never as proof of anything. It ignores the triggering run's result, outputs and branch name.
- Everything `merge` decides on comes from GitHub's API or from `main`: reviews, changed files, `OWNERS.yaml`. The pull request's files are only read as data by `knowledge-cli`, which is downloaded from a pinned release and checked by sha256. Nothing from the pull request is executed.
- Only the job that merges has write permission. It never checks out the pull request. The job that does check it out (to validate it) can only read, and no workflow here uses a cache, so there is nothing to poison for `main`.
- The repository has no secrets.
- `merge` runs one at a time; a burst of triggers collapses into one queued run.

The worst a malicious pull request can do is make `merge` evaluate pull requests more often.

## Read API

On every merge, `pages` publishes the content as JSON on GitHub Pages:

```
https://open-macaron.github.io/knowledge/v1/
├── index.json                        commit, and every folder's rules (id, title, severity, globs) and lessons
├── global.json
├── projects/<project>.json
└── repos/<owner>/<repo>.json
```

Each file holds a folder's `overview`, `rules` and `lessons`, with their full text. The bot combines `global` → its project → its repository. The shapes are JSON Schemas in [`open-macaron/macaron`](https://github.com/open-macaron/macaron/tree/main/packages/contracts/schemas) (`knowledge-*.schema.json`).

## Checking locally

```sh
# The release pinned in .github/workflows (KNOWLEDGE_CLI_URL):
curl -fsSLO https://github.com/open-macaron/macaron/releases/download/knowledge-cli-<sha>/knowledge-cli.mjs
node knowledge-cli.mjs validate .                  # layout, front matter, sections, globs
GITHUB_TOKEN=$(gh auth token) node knowledge-cli.mjs validate . --check-logins
```

`knowledge-cli` lives in [`open-macaron/macaron`](https://github.com/open-macaron/macaron/tree/main/packages/knowledge-cli). Each release has a build provenance attestation: `gh attestation verify knowledge-cli.mjs -R open-macaron/macaron`.
