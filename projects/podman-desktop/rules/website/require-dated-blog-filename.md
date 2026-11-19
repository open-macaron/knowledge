---
title: Name a blog post website/blog/YYYY-MM-DD-slug.mdx after its publication date
severity: warning
applies_to: ["website/blog/**"]
exclude: ["website/blog/img/**"]
---
## Check
Is an added blog post file not named `YYYY-MM-DD-<slug>.mdx`?

## Violation
`website/blog/podman-desktop-release-1.30.mdx`: Docusaurus takes the publication date from the file name, and posts don't set `date:`. Quote the post's first line.

## Not a violation
- `website/blog/2026-11-19-podman-desktop-release-1.30.mdx`.
- Images and other assets.
- Edits to existing posts.

## Examples
```text
❌ website/blog/release-1.30.mdx
✅ website/blog/2026-11-19-release-1.30.mdx
```
