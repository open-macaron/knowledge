---
title: Commit from workflows as the actor's noreply address, not a personal email
severity: warning
applies_to: [".github/workflows/**"]
---
## Check
Does an added or modified line set a git or commit identity with a real email address?

## Violation
`git config user.email "someone@redhat.com"`, or a personal address in an action's `author`/`committer` input.

## Not a violation
- `"${{ github.actor_id }}+${{ github.actor }}@users.noreply.github.com"`, or a bot's noreply address.
- Lines that were not added or modified by the change.

## Examples
```yaml
# ❌
git config --local user.email "someone@redhat.com"
# ✅ (next-build.yaml)
git config --local user.email "${{ github.actor_id }}+${{ github.actor }}@users.noreply.github.com"
```
