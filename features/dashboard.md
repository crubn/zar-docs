---
title: Dashboard
description: The ZAR dashboard — per-repo settings, the activity feed, and the run view that shows exactly what happened.
---

# Dashboard

The dashboard at **[app.zarlabs.tech](https://app.zarlabs.tech)** is where you manage repositories, change settings that don't live in `.zar.yml`, and inspect exactly what ZAR did on every event.

## Repositories

After you [connect a repo](/getting-started/installation), it appears in your repository list. From there you can open its settings and activity.

## Per-repo settings

The dashboard exposes the operational toggles that are intentionally **not** in `.zar.yml`:

- [Auto-commit](/features/auto-commit) (`auto_commit_enabled`)
- [CI gate](/features/ci-gate) (`ci_gate_enabled`) and its `required_doc_freshness` threshold
- [Dry-run](/configuration/trigger-modes#dry-run) and debug mode
- `use_updates_branch`, `disable_pr`, comment toggle
- [Cross-repo docs](/features/cross-repo-docs) settings
- Trigger mode, target branch, PR size, and style guide (also settable in [`.zar.yml`](/configuration/zar-yml))

See the [Settings reference](/reference/settings) for every field.

## Activity feed

The **Activity** view lists recent runs across your repos — a human-readable stream of "ZAR opened PR #123", "CI gate blocked", "dry run — no changes", and so on. It's the quick answer to "what has ZAR been doing?".

## Run view

![The dashboard run view — a step-by-step timeline of one webhook delivery](/assets/dashboard.svg)

*Illustrative mockup.*

This is the most useful debugging tool. **One run = one webhook delivery** from GitHub, identified by its delivery ID. Open a run to see:

- **Header** — repository, event type, PR number, timestamp.
- **Summary** — a one-line outcome (e.g. "PR created: #456", "Subscription required", "CI gate blocked").
- **Timeline** — the step-by-step path the event took, such as:
  - `github.received` — webhook arrived
  - `settings.loaded` — repo settings fetched
  - `diff.summarized` — changed files classified (code/doc/skipped)
  - `update.started` — doc pipeline began
  - `pr.created` / `pr.failed` / `rate_limit.blocked` / `subscription.required`
- **Stats** — counts of changed files, code files, docs suggested vs. docs updated.
- **Flags** — whether CI gate, auto-commit, and dry-run were in effect, and whether anything was committed.
- **Error** — the message, if the run failed.
- **PR link** — to the docs PR, when one was created.

If you ever wonder *why* ZAR did (or didn't) do something on a PR, the run view's timeline almost always has the answer — which gate fired, what was skipped, and why.

## Validation tools

Under **Advanced**, the dashboard offers:

- **Validate GitHub App** — checks that the App ID, private key, and webhook secret are consistent.
- **Send test webhook** — emits a synthetic event so you can confirm the pipeline end-to-end without making a real commit.

Both are described in the [Quickstart](/getting-started/quickstart#step-3--optional-validate-the-connection).

## Next steps

- **[Settings reference](/reference/settings)** — every dashboard field.
- **[How ZAR works](/concepts/how-it-works)** — what the timeline steps correspond to.
- **[Troubleshooting](/troubleshooting)** — using runs to diagnose problems.
