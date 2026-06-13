---
title: Cross-repo docs
description: Keep documentation in a separate repository in sync with code changes in another.
---

# Cross-repo docs

Some teams keep documentation in a **separate repository** from the code — a dedicated `docs` repo, a handbook, or a Mintlify/Nextra site. Cross-repo docs lets ZAR analyze code changes in one repository and open documentation PRs against a **different** one.

This is a **Team / Enterprise** feature. See [Plans](/plans).

## How it works

When cross-repo docs is enabled for a code repository, ZAR:

1. analyzes diffs in the **code repo** exactly as usual, then
2. proposes and opens the documentation PR in the configured **docs repo**, under a path you specify, against a branch you choose.

Your code stays in one place; the docs that describe it stay accurate in another.

## Configure it

Cross-repo settings are **dashboard-only** (not in `.zar.yml`):

| Setting | What it is | Default |
|--|--|--|
| `cross_repo_enabled` | Turn cross-repo docs on for this code repo | `false` |
| `docs_repo` | The target docs repository, as `owner/repo` | — |
| `docs_repo_path` | Folder within the docs repo where ZAR writes | `/docs` |
| `docs_repo_branch` | Branch in the docs repo to open PRs against | `main` |

`docs_repo` is validated as `owner/repo`; an invalid value is rejected.

## Requirements

- **ZAR must be installed on both repositories** — the code repo (to read diffs) and the docs repo (to open PRs). Install it on the docs repo the same way you did the first one.
- The docs repo needs Markdown/MDX files for ZAR to edit. As everywhere, ZAR edits existing docs rather than scaffolding new ones.

## Good fits

- A monorepo of services with a central `docs/` site repo.
- A public product whose handbook or Mintlify site lives in its own repo (like this one).
- Multiple code repos feeding a single documentation hub.

## Next steps

- **[Settings reference](/reference/settings)** — the cross-repo fields.
- **[MDX & multilingual](/features/mdx-and-multilingual)** — if your docs repo is a Nextra/MDX site.
- **[Plans](/plans)** — availability.
