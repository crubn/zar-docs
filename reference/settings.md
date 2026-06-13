---
title: Settings reference
description: Every ZAR per-repository setting — type, default, range, where it can be set, and what it does.
---

# Settings reference

The complete list of per-repository settings. For how these combine, see [Configuration overview](../configuration/overview.md); for the file you commit, see the [`.zar.yml` reference](../configuration/zar-yml.md).

**Where:** `.zar.yml` = settable in the repo file · `dashboard` = settable in the dashboard/database. The [precedence rule](../configuration/overview.md#precedence-dashboard-wins) is: dashboard (if set) → `.zar.yml` (if set) → default.

## Documentation behavior

These shape how docs are produced and can live in `.zar.yml`.

| Setting | Type | Default | Values / range | Where | What it does |
|--|--|--|--|--|--|
| `trigger_mode` (`.zar.yml`: `trigger`) | string | `on_significant_change` | `every_commit`, `on_significant_change`, `daily`, `on_merge` | `.zar.yml` + dashboard | When ZAR opens a docs PR. See [Trigger modes](../configuration/trigger-modes.md). |
| `batch_hours` | int | `24` | `1`–`168` (clamped) | `.zar.yml` + dashboard | Hours to batch pending changes before a PR. |
| `min_confidence` (`.zar.yml`: `threshold`) | float | `0.6` | `0.0`–`1.0` (clamped) | `.zar.yml` + dashboard | Confidence required to act on a change. |
| `target_branch` | string | `main` | any branch | `.zar.yml` + dashboard | The branch ZAR analyzes. |
| `pr_size` | string | `small` | `small`, `full` | `.zar.yml` + dashboard | `small` ≈ 1–3 files; `full` = all changed docs. |
| `style_guide` | string | empty | free text | `.zar.yml` + dashboard | Writing style passed to Claude. See [Style guide](../configuration/style-guide.md). |
| `notify_slack` | string / `false` | disabled | Slack webhook URL | `.zar.yml` only | Optional Slack notification (operator-dependent). |

> `min_confidence` is currently exposed via `.zar.yml` (`threshold`) and the API/database; it may not have a dedicated dashboard field in every build. Set it via `.zar.yml` for portability.

## Write-back & gating

Operational switches — **dashboard-only** so a PR can't flip them.

| Setting | Type | Default | Where | What it does |
|--|--|--|--|--|
| `auto_commit_enabled` | bool | `false` | dashboard | Allow direct commits. Also requires the `DOCAGENT_WRITE_COMMITS` env var. See [Auto-commit](../features/auto-commit.md). |
| `ci_gate_enabled` | bool | `false` | dashboard | Block merges when docs look stale. See [CI gate](../features/ci-gate.md). |
| `required_doc_freshness` | float | high (≈`0.9`) | dashboard | CI gate threshold. If ZAR proposes any doc change, freshness falls below this and the gate blocks. |
| `dry_run` | bool | `false` | dashboard | Report only — never commit, never block. |
| `use_updates_branch` | bool | `true` | dashboard | Commit to a dedicated `zar-updates/…` branch (vs. the PR head). |
| `disable_pr` | bool | `false` | dashboard | Hard off switch — no docs PRs, no commits. |
| `comment_enabled` | bool | `true` | dashboard | Post the status comment on PRs. |
| `debug_mode` | bool | `false` | dashboard | Verbose logs + an extra "Debug Info" comment. |

## Cross-repo docs

Dashboard-only. See [Cross-repo docs](../features/cross-repo-docs.md). **Team / Enterprise** only.

| Setting | Type | Default | What it does |
|--|--|--|--|
| `cross_repo_enabled` | bool | `false` | Open docs PRs in a separate repository. |
| `docs_repo` | string | — | Target docs repo, `owner/repo` (validated). |
| `docs_repo_path` | string | `/docs` | Folder in the docs repo to write to. |
| `docs_repo_branch` | string | `main` | Branch in the docs repo to target. |

## Validation & clamping

- `batch_hours` → clamped to `1`–`168`; non-integer values are ignored (default kept).
- `min_confidence` / `threshold` and `required_doc_freshness` → clamped to `0.0`–`1.0`.
- `trigger_mode` → invalid values fall back to `on_significant_change`.
- `pr_size` → invalid values fall back to `small`.
- `target_branch`, `docs_repo_path`, `docs_repo_branch` → empty values reset to their defaults (`main`, `/docs`, `main`).
- `docs_repo` → must match `owner/repo`, otherwise rejected.

## Related

- [Configuration overview](../configuration/overview.md) — precedence and recommended setup.
- [`.zar.yml` reference](../configuration/zar-yml.md) — the version-controlled file.
- [Environment variables](environment-variables.md) — operator-level configuration (self-hosting).
