---
title: Auto-commit
description: Let ZAR commit documentation fixes directly instead of opening a PR — and the two locks that gate it.
---

# Auto-commit

By default ZAR proposes doc changes as a [pull request](/features/pr-feedback). **Auto-commit** lets it skip the PR and write the fix directly. It's powerful and intentionally hard to enable by accident.

## Two locks, both required

ZAR will commit doc updates only when **both** of these are true:

1. **The repo setting `auto_commit_enabled` is on.** Toggle it with `@docagent enable auto-commit` or in the dashboard.
2. **The operator has set `DOCAGENT_WRITE_COMMITS`** in the deployment's environment.

If either is missing, ZAR falls back to opening a PR. This two-lock design means an end user can't turn on writes without the operator's deployment also allowing it — and the operator can globally disable all writes regardless of per-repo settings. See [Safety & guarantees](/concepts/safety-and-guarantees#2-zar-never-writes-by-default).

On the hosted app at [app.zarlabs.tech](https://app.zarlabs.tech), the environment lock is managed for you, so the dashboard/command toggle is the switch you control.

## Enable it

```text
@docagent enable auto-commit
```

(Author needs write access.) Disable it the same way:

```text
@docagent disable auto-commit
```

You can confirm the current state with `@docagent status`.

## Where commits land

With `use_updates_branch` on (the default), ZAR commits to a dedicated `zar-updates/…` branch and surfaces it as a PR rather than pushing straight to your protected branch. If you turn `use_updates_branch` off, ZAR commits to the PR head branch instead. Either way, branch protection rules still apply — ZAR can't push where GitHub won't let it.

## When to use it

Auto-commit suits teams that have watched ZAR's PRs for a while and trust the output, especially with a [style guide](/configuration/style-guide) dialed in. A good rollout:

1. Run with PRs only for a few weeks.
2. Add a `style_guide` and refine it.
3. Turn on auto-commit on one repo, with `use_updates_branch` on, so commits still arrive as a reviewable branch.
4. Expand once you're comfortable.

## Interaction with other settings

- **Dry-run wins.** A repo in [dry-run](/configuration/trigger-modes#dry-run) never commits, even with auto-commit on.
- **`disable_pr`** stops ZAR from opening PRs or committing entirely — a hard off switch.
- Auto-commit doesn't change [significance or trigger](/configuration/trigger-modes) decisions; it only changes *how* an approved change is delivered.

## Next steps

- **[Commands](/features/commands)** — `enable auto-commit`, `status`.
- **[Safety & guarantees](/concepts/safety-and-guarantees)** — why writes need two locks.
- **[Environment variables](/reference/environment-variables)** — `DOCAGENT_WRITE_COMMITS` (self-hosting).
