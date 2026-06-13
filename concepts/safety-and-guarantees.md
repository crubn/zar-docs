---
title: Safety & guarantees
description: Exactly what ZAR will and will not do to your repository, and the mechanisms that enforce it.
---

# Safety & guarantees

ZAR touches your repository, so it's built to be conservative by default and predictable under failure. This page lists the guarantees and the mechanism behind each one, so you can trust it before you turn anything on.

## 1. Every webhook is verified

ZAR verifies the `X-Hub-Signature-256` HMAC-SHA256 signature on every incoming webhook against your configured secret. A missing or invalid signature is rejected with `401` and never processed. An attacker who doesn't know your webhook secret cannot make ZAR act.

## 2. ZAR never writes by default

Out of the box, ZAR only **comments** and **opens pull requests**. Committing directly to your branches requires **two** independent opt-ins:

1. The repository setting `auto_commit_enabled` must be on, **and**
2. The operator must set the `DOCAGENT_WRITE_COMMITS` environment variable.

If either is missing, ZAR cannot commit — it falls back to a PR. This is intentional: a single misconfiguration can't cause unexpected writes. See [Auto-commit](/features/auto-commit).

## 3. Suggestions are grounded, never invented

Claude is constrained by a system prompt that forbids inventing facts, documenting private/internal details, or manufacturing trivial edits. ZAR feeds it the actual diff and the tree-sitter symbol summary, and asks for the **smallest** change that makes the docs true. When no doc change is warranted, the expected output is an empty list, and ZAR stays silent.

## 4. Dry-run is a hard guarantee

When a repository is in **dry-run** mode, ZAR will:

- never commit, and
- never block a merge (the CI gate reports but does not fail the check).

Dry-run forces auto-commit and the CI gate off for that repo regardless of their individual settings. It's the safe way to see what ZAR *would* do. See [Trigger modes](/configuration/trigger-modes) and [CI gate](/features/ci-gate).

## 5. The CI gate fails safe

If the CI gate is enabled and ZAR can't confidently parse the model's assessment, it **blocks** rather than waving the merge through. Conservative by design: a false "docs needed" is recoverable; a silent stale-docs merge is the thing the gate exists to prevent.

## 6. Human edits are never destroyed

ZAR maintains its docs PR on a dedicated `zar-updates/…` branch. If it hits a conflict while updating that branch, it checks whether a human pushed commits to it. If a person edited the branch, ZAR reuses the existing PR and leaves your work alone. Only branches that contain solely ZAR's own commits are reset and retried.

## 7. Bad edits are rejected, not shipped

Two post-processing checks protect your docs:

- **End-of-file append rejection.** ZAR refuses edits that just dangle new content after a file's natural end (for example, after a License footer). No edit is better than malformed Markdown.
- **MDX integrity check.** For `.mdx` files, ZAR verifies that YAML frontmatter keys and `import`/`export` statements survive the edit. If an edit would strip them — and break a Nextra build — ZAR rejects it. See [MDX & multilingual](/features/mdx-and-multilingual).

## 8. No feedback loops

ZAR detects commits and pushes authored by bots (including its own bot account) and skips them. It won't react to its own doc commits and spiral into a loop.

## 9. Deliveries are idempotent

Each run is keyed by GitHub's webhook **delivery ID**. If GitHub retries a delivery, ZAR updates the same run record instead of duplicating work — one delivery, one run.

## 10. Concurrent events are serialized per repo

ZAR serializes writes per repository so two near-simultaneous events can't race to create duplicate docs PRs. (On a single instance this is an in-process lock; multi-replica deployments should account for this — see [Self-hosting](/self-hosting/overview).)

## 11. Failures degrade gracefully

If something goes wrong mid-pipeline, ZAR records the error on the run, sets the commit status accordingly, and where possible falls back to a comment instead of crashing. A failed run is visible in the [dashboard](/features/dashboard), not silent.

## 12. Cost and scope are bounded

ZAR caps how much it analyzes and how often:

- A maximum number of code files per event, and per-file/total character limits on what's sent to Claude.
- A per-PR analysis rate limit (default: a small number of analyses per PR per hour) to control cost and noise.

Large changes are analyzed up to the cap, and ZAR notes in the PR body when it only covered part of a very large change. Limits are configurable by the operator — see [Environment variables](/reference/environment-variables).

## The short version

| Guarantee | Default |
|--|--|
| Commits to your branches | **Off** (needs setting + env var) |
| Blocks merges | **Off** (CI gate is opt-in; never blocks in dry-run) |
| Invents undocumented features | **Never** |
| Acts on unverified webhooks | **Never** |
| Reacts to its own commits | **Never** |
| Records every run | **Always** |

When in doubt, run a repo in **dry-run** and read the [dashboard](/features/dashboard) runs. You'll see exactly what ZAR would do before granting it the ability to do it.
