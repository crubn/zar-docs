---
title: Configuration overview
description: Where ZAR settings live, which ones go in .zar.yml versus the dashboard, and how they combine.
---

# Configuration overview

ZAR reads configuration from two places:

1. **A `.zar.yml` file** in your repository root — version-controlled, reviewed in PRs, lives with the code.
2. **The dashboard / database** — per-repo settings you toggle at [app.zarlabs.tech](https://app.zarlabs.tech).

Some settings exist in both; some only in one. This page explains who wins when they disagree, and which setting belongs where.

## Precedence: dashboard wins

When a setting can be configured in both places and is explicitly set in the dashboard, **the dashboard value wins**. If the dashboard leaves it unset, ZAR falls back to `.zar.yml`, and then to the built-in default.

```
dashboard value (if set)  ▶  .zar.yml value (if set)  ▶  built-in default
```

Example: your `.zar.yml` says `trigger: daily` but the dashboard has trigger mode set to `on_significant_change`. ZAR uses `on_significant_change`. If the dashboard trigger were left unset, ZAR would use `daily` from `.zar.yml`.

## What goes where

### Settable in `.zar.yml` (and most also in the dashboard)

These describe *how docs should be produced* and are natural to version with the repo:

| Setting | Also in dashboard? |
|--|--|
| `trigger` (trigger mode) | Yes |
| `batch_hours` | Yes |
| `threshold` (confidence) | Yes (as `min_confidence`) |
| `target_branch` | Yes |
| `pr_size` | Yes |
| `style_guide` | Yes |
| `notify_slack` | No (`.zar.yml` only) |

Full schema and an example: **[`.zar.yml` reference](/configuration/zar-yml)**.

### Dashboard / database only

These are operational toggles and safety switches, kept out of the repo so a PR can't silently flip them:

| Setting | What it does |
|--|--|
| `auto_commit_enabled` | Allow ZAR to commit doc updates (also needs the `DOCAGENT_WRITE_COMMITS` env var). |
| `ci_gate_enabled` | Block merges when docs look stale. |
| `required_doc_freshness` | The CI gate threshold. |
| `dry_run` | Report only — never commit, never block. |
| `debug_mode` | Verbose logs plus an extra "Debug Info" comment. |
| `use_updates_branch` | Commit to a dedicated `zar-updates/…` branch vs. the PR head. |
| `disable_pr` | Stop ZAR from opening docs PRs or committing. |
| `comment_enabled` | Post the status comment on PRs. |
| `cross_repo_enabled`, `docs_repo`, `docs_repo_path`, `docs_repo_branch` | [Cross-repo docs](/features/cross-repo-docs). |

The complete table of every setting, with types, defaults, and ranges, is in the **[Settings reference](/reference/settings)**.

## New-repository defaults

When ZAR first sees a repository, it starts conservative:

| Setting | Default |
|--|--|
| `trigger_mode` | `on_significant_change` |
| `batch_hours` | `24` |
| `pr_size` | `small` |
| `target_branch` | `main` |
| `min_confidence` | `0.6` |
| `auto_commit_enabled` | `false` |
| `ci_gate_enabled` | `false` |
| `dry_run` | `false` |
| `use_updates_branch` | `true` |

So out of the box: ZAR watches `main`, acts only on significant changes, opens small PRs, and never commits or blocks.

## A recommended starting point

1. Leave everything at defaults and open a few PRs to see ZAR's suggestions.
2. Add a `.zar.yml` with a `style_guide` so edits match your voice.
3. When you trust the output, enable the [CI gate](/features/ci-gate) — start in [dry-run](/configuration/trigger-modes#dry-run) to preview.
4. Only after that, consider [auto-commit](/features/auto-commit).

## Next steps

- **[`.zar.yml` reference](/configuration/zar-yml)** — the file you commit to your repo.
- **[Trigger modes](/configuration/trigger-modes)** — when ZAR speaks up.
- **[Settings reference](/reference/settings)** — every setting, every default.
