---
title: Trigger modes
description: Control when ZAR opens a documentation PR — the four trigger modes, the significance filter, batching, and dry-run.
---

# Trigger modes

The trigger mode decides *when* ZAR opens a documentation PR. Combined with the significance filter, it's the main control over how chatty ZAR is.

Set it in [`.zar.yml`](zar-yml.md) (`trigger:`) or the dashboard. Default: `on_significant_change`.

> A commit status (`zar/docs`) is posted on **every** analyzed commit regardless of trigger mode. The trigger mode only governs whether a *PR* is opened.

## The four modes

### `on_significant_change` (default)

Opens a docs PR when the change is **significant** (see below) **and** clears the confidence threshold. If a change isn't significant yet but others are pending, ZAR can still open a PR once the [batch window](#batching) elapses. This is the recommended default: responsive without nagging.

### `every_commit`

Acts on every analyzed commit that clears confidence, as long as it isn't purely trivial/test-only/config-only. Use this when docs accuracy is critical and you want the tightest loop. Expect more PRs.

### `daily`

Batches changes and opens at most one docs PR per `batch_hours` window (default 24h). Significant changes are collected and surfaced together when the window elapses. Good for busy repos that don't want a PR per push.

### `on_merge`

Acts only when a pull request is **merged**, not while it's open. Use this if you'd rather keep docs changes off in-flight PRs and update them once work lands on the base branch.

## The significance filter

Before any mode opens a PR, ZAR asks: *is this change even doc-worthy?* A change is **significant** if it includes any of:

- a **new public symbol** — a function, class, method, or API route (private/underscored symbols don't count);
- a **deleted or renamed file**;
- a **signature change** — symbols both removed and added in the same file;
- a **meaningful diff** of at least ~10 lines (whitespace-only lines are excluded).

A change is **skipped** (not significant) when it is:

- **test-only** — every changed file looks like a test (`_test.`, `test_`, `.spec.`, `/tests/`, `/__tests__/`, …);
- **config-only** — every changed file is config (`.json`, `.yaml`/`.yml`, `.toml`, `.ini`, `.env`, `.cfg`, files under `config/` or `.github/`);
- **trivial** — a small diff with no public symbols, deletions, renames, or signature changes.

When a change is skipped, ZAR posts a green `zar/docs` status and does nothing else. This is why a PR that only edits tests or CI config won't get a ZAR comment — by design.

## Confidence threshold

Significance answers *"did something doc-worthy change?"*; confidence answers *"how sure is ZAR?"*. The `threshold` (`.zar.yml`) / `min_confidence` (dashboard) value, default `0.6`, gates borderline changes. Raise it to suppress marginal suggestions; lower it to catch more.

This is distinct from the CI gate's `required_doc_freshness` threshold, which governs *blocking* — see [CI gate](../features/ci-gate.md).

## Batching

`batch_hours` (default `24`, range `1`–`168`) controls how long ZAR holds pending changes before opening a PR:

- In **`daily`**, the PR opens when the window elapses (or immediately on a significant, confident change if nothing is pending).
- In **`on_significant_change`**, a significant + confident change opens a PR immediately; otherwise, pending changes are flushed once they age past `batch_hours`.

Pending changes are tracked across commits and cleared when the PR is finally opened.

## Forcing a run

To bypass trigger mode and significance entirely, comment on a PR:

```text
@docagent update docs now
```

This forces ZAR to analyze and propose changes immediately, regardless of mode. See [Commands](../features/commands.md).

## Dry-run

**Dry-run** is a per-repo dashboard switch that makes ZAR report only:

- it never commits, and
- it never blocks a merge (the CI gate evaluates and comments but doesn't fail).

It's the safest way to preview behavior on a real repo before enabling the CI gate or auto-commit. See [Safety & guarantees](../concepts/safety-and-guarantees.md#4-dry-run-is-a-hard-guarantee).

## Choosing a mode

| You want… | Use |
|--|--|
| Sensible default, responsive but quiet | `on_significant_change` |
| Tightest possible loop, docs are critical | `every_commit` |
| One batched PR per day on a busy repo | `daily` |
| Update docs only after work lands | `on_merge` |
| To watch without any risk | any mode + **dry-run** |

## Next steps

- **[`.zar.yml` reference](zar-yml.md)** — set `trigger`, `batch_hours`, `threshold`.
- **[CI gate](../features/ci-gate.md)** — turn stale docs into a failing check.
- **[How ZAR works](../concepts/how-it-works.md)** — where significance and trigger policy sit in the pipeline.
