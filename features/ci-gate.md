---
title: CI gate
description: Block a merge when documentation looks stale — how to enable it, what it posts, and how it fails safe.
---

# CI gate

The CI gate turns documentation accuracy into a merge requirement. When enabled, ZAR checks a pull request's docs and, if they look stale, **blocks the merge** the same way a failing test would.

It's **off by default**. Turn it on only when you trust ZAR's suggestions on a repo — and consider previewing in [dry-run](/configuration/trigger-modes#dry-run) first.

## Enable it

Either from a PR comment:

```text
@docagent enable ci-gate
```

or in the dashboard (`ci_gate_enabled`). Disable the same way:

```text
@docagent disable ci-gate
```

The author needs **write** access to run the command. See [Commands](/features/commands).

## What it does on a PR

When the gate is enabled and a PR is updated (`synchronize` / `ready_for_review`), ZAR evaluates whether the docs are still accurate. If they're **not**, ZAR:

- **comments** on the PR with the proposed documentation edits,
- adds two labels: **`docs-needed`** and **`ci-blocked`**,
- creates a failing check run named **`docagent/ci-gate`** with the title "ZAR CI Gate: docs needed".

If the docs **are** accurate, ZAR removes those labels (if present), posts a short "gate passed" comment, and lets the merge proceed.

![A blocked ZAR CI gate on a pull request, with docs-needed and ci-blocked labels](/assets/ci-gate-check.svg)

*Illustrative mockup.*

## How the decision is made

ZAR computes a *doc freshness* score for the PR and compares it to the `required_doc_freshness` threshold:

- If ZAR proposes **no** doc changes, freshness is high and the gate passes.
- If ZAR proposes **any** doc change, freshness drops below the (high, ~0.9) threshold and the gate blocks.

In practice: **if ZAR thinks the docs need an edit for this PR, the gate blocks until that's resolved.** You can tune `required_doc_freshness` in the dashboard if you want a looser gate.

## It fails safe

If ZAR can't confidently parse the model's assessment, it **blocks** rather than letting the PR through. A spurious "docs needed" is easy to clear; a silently merged stale-docs PR is exactly what the gate exists to stop.

## Dry-run never blocks

If the repo is in [dry-run](/configuration/trigger-modes#dry-run), the gate still evaluates and can comment, but it **never** fails the check or blocks the merge. Use this to see how often the gate *would* fire before you let it block real merges.

## Make it required (optional)

The gate creates a standard GitHub check run (`docagent/ci-gate`). To enforce it, add it to your branch protection rules: **Settings → Branches → Branch protection → Require status checks to pass**. Now a stale-docs PR can't be merged until ZAR is satisfied or the gate is cleared.

## Clearing a block

To unblock a PR that the gate has flagged:

1. Merge ZAR's proposed docs edit (from the comment or the docs PR), or update the docs yourself, then
2. push to the PR so ZAR re-evaluates. When freshness clears the threshold, ZAR removes the labels and the check turns green.

Or, if the flag is wrong, disable the gate (`@docagent disable ci-gate`) or adjust the threshold.

## Reference

| Thing ZAR creates | Name |
|--|--|
| Commit status context | `zar/docs` |
| Check run | `docagent/ci-gate` |
| Labels | `docs-needed`, `ci-blocked` |

Plan note: the CI gate is available on **Team** and **Enterprise** plans. See [Plans](/plans).

## Next steps

- **[Trigger modes](/configuration/trigger-modes)** — and dry-run for safe previews.
- **[PR feedback](/features/pr-feedback)** — the comment and status the gate builds on.
- **[Settings reference](/reference/settings)** — `ci_gate_enabled`, `required_doc_freshness`.
