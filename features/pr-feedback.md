---
title: PR feedback
description: The comments, commit statuses, and docs PRs ZAR produces on a pull request.
---

# PR feedback

ZAR's primary output is feedback on a pull request. Three things can appear, depending on what changed and how you've configured ZAR.

## 1. The `zar/docs` commit status

On every analyzed commit, ZAR posts a commit status with the context name **`zar/docs`**. It's the at-a-glance signal:

| State | Meaning |
|--|--|
| `success` | Docs are in sync, or ZAR opened a docs PR / committed an update. |
| `pending` | Docs need an update and ZAR is batching it (e.g. "Docs need update — N changes detected"). |
| `failure` | ZAR couldn't proceed — typically a subscription is required, or analysis errored. |

Because it's a standard commit status, you can see it in the PR's checks and even require it in branch protection (related but distinct from the [CI gate](/features/ci-gate)).

## 2. A comment on the pull request

When ZAR has something to propose and comments are enabled (`comment_enabled`, on by default), it posts a comment on the PR. The comment:

- summarizes what changed in the code that affects docs,
- shows the proposed Markdown edits (as diffs),
- links to the docs PR that contains the change.

Comments are posted as the ZAR app, so they're easy to spot and filter. ZAR won't react to its own comments.

![A ZAR comment proposing a documentation update on a pull request](/assets/pr-comment.svg)

*Illustrative mockup.*

## 3. A documentation pull request

The substantive output is a separate **docs PR**:

- It lives on a dedicated branch named `zar-updates/…` and targets your base branch.
- It contains the minimal Markdown patches Claude proposed.
- ZAR keeps **at most one open docs PR per base branch** — new suggestions update or supersede the existing one rather than piling up.
- The PR title starts with `docs:` and the body explains the change in plain language.

You review and merge it like any other PR. ZAR never force-pushes over your work, and if a human edits the docs branch, ZAR preserves those edits (see [Safety & guarantees](/concepts/safety-and-guarantees#6-human-edits-are-never-destroyed)).

## When you'll see nothing

ZAR stays quiet when:

- the change isn't [significant](/configuration/trigger-modes#the-significance-filter) (test-only, config-only, or trivial);
- the docs already match the change;
- the [trigger mode](/configuration/trigger-modes) is batching the change for later;
- a [rate limit](/concepts/safety-and-guarantees#12-cost-and-scope-are-bounded) has been hit for this PR.

A green `zar/docs` status with no comment usually means "docs look fine" — that's success, not silence-by-error. Check the [dashboard run](/features/dashboard) if you want the details.

## Large changes

For very large diffs, ZAR analyzes up to its configured file cap and notes in the PR body when it only covered part of the change, so you know to glance at the rest.

## Next steps

- **[CI gate](/features/ci-gate)** — turn the `pending` status into a merge blocker.
- **[Auto-commit](/features/auto-commit)** — skip the docs PR and commit fixes directly.
- **[Dashboard](/features/dashboard)** — see the full timeline behind any PR's feedback.
