---
title: .zar.yml reference
description: The version-controlled ZAR configuration file — every supported key, with types, defaults, and a complete example.
---

# `.zar.yml` reference

Place a `.zar.yml` file in your repository root to version your ZAR settings alongside your code. It's optional — ZAR works with sensible defaults — but it's the cleanest way to keep configuration reviewable.

Remember the [precedence rule](/configuration/overview#precedence-dashboard-wins): an explicit dashboard setting overrides the same key in `.zar.yml`.

## Complete example

```yaml
# .zar.yml — ZAR repository configuration
# Every key is optional. Dashboard settings override these when set.

trigger: on_significant_change   # every_commit | on_significant_change | daily | on_merge
batch_hours: 24                  # 1–168; how long to batch before opening a PR
threshold: 0.6                   # 0.0–1.0; confidence required to act
target_branch: main              # branch ZAR watches
pr_size: small                   # small (1–3 files) | full (all changed docs)

style_guide: |                   # natural-language writing style (optional)
  Write like Stripe's API docs.
  Short sentences. One code example per endpoint.
  Use tables for parameters. Never use marketing language.

notify_slack: false              # Slack webhook URL, or false to disable
```

## Keys

### `trigger`

When ZAR opens a documentation PR. Hyphens and underscores are both accepted (`on-significant-change` = `on_significant_change`).

- **Type:** string · **Default:** `on_significant_change`
- **Values:** `every_commit`, `on_significant_change`, `daily`, `on_merge`
- An unrecognized value falls back to the default. Full behavior: [Trigger modes](/configuration/trigger-modes).

### `batch_hours`

How long ZAR batches pending changes before opening a PR. Applies to `daily` and to the batching fallback of `on_significant_change`.

- **Type:** integer (hours) · **Default:** `24`
- **Range:** clamped to `1`–`168` (1 hour to 7 days).

### `threshold`

The confidence ZAR requires before it acts on a change. Lower = more eager, higher = more conservative.

- **Type:** float · **Default:** `0.6`
- **Range:** clamped to `0.0`–`1.0`
- Maps to the dashboard's `min_confidence`. (The CI gate uses a separate `required_doc_freshness` threshold — see [CI gate](/features/ci-gate).)

### `target_branch`

The branch ZAR analyzes. Pushes and PRs against other branches are ignored.

- **Type:** string · **Default:** `main`
- Whitespace is trimmed; an empty value resets to `main`.

### `pr_size`

How much one docs PR is allowed to contain.

- **Type:** string · **Default:** `small`
- **Values:** `small` (roughly 1–3 files) · `full` (all changed docs)
- An unrecognized value falls back to `small`.

### `style_guide`

Natural-language instructions that shape ZAR's writing — tone, structure, examples, terminology. Free text; a YAML block scalar (`|`) is handy for multiple lines.

- **Type:** string · **Default:** empty
- See [Style guide](/configuration/style-guide) for what works well.

### `notify_slack`

A Slack incoming-webhook URL to notify on doc updates. Set to `false` (or omit) to disable. This key is **`.zar.yml`-only** — there's no dashboard equivalent. If your instance's operator hasn't configured outbound notifications, treat this as best-effort.

- **Type:** string (webhook URL) or `false` · **Default:** disabled

## Parsing behavior

- **Unknown keys** are ignored (logged at debug level), so a typo won't break the file — but it also won't take effect. Double-check key names against this page.
- **A malformed file** (invalid YAML) is logged as a warning and ZAR falls back to defaults rather than failing the run.
- **An empty file** is equivalent to all defaults.
- **Numeric values out of range** are clamped to the valid range rather than rejected.

## What you cannot set here

Safety and operational switches are intentionally **dashboard-only** and cannot be set from `.zar.yml`: `auto_commit_enabled`, `ci_gate_enabled`, `required_doc_freshness`, `dry_run`, `debug_mode`, `use_updates_branch`, `disable_pr`, `comment_enabled`, and the cross-repo settings. This keeps a pull request from silently turning on commits or flipping the merge gate.

See the [Settings reference](/reference/settings) for those.

## Next steps

- **[Trigger modes](/configuration/trigger-modes)** — what each `trigger` value does.
- **[Style guide](/configuration/style-guide)** — write a `style_guide` that works.
- **[Configuration overview](/configuration/overview)** — how `.zar.yml` and the dashboard combine.
