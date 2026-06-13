---
title: Introduction
description: What ZAR is, the problem it solves, and how it fits into your GitHub workflow.
---

# Introduction

**ZAR is a GitHub App that keeps your documentation accurate as your code changes.**

Every team has felt documentation drift. A pull request renames a function, adds a config flag, or changes a default — and the `README` and `docs/` quietly fall out of date. Nobody notices until a new teammate follows the docs into a wall.

ZAR turns "we should update the docs" into something that happens automatically, at review time, where it belongs.

## What ZAR does

When code changes in a repository ZAR is installed on, it:

1. **Reads the diff** the way a careful reviewer would — using [tree-sitter](https://tree-sitter.github.io/) to extract which functions, classes, and signatures actually changed.
2. **Decides whether the change matters for docs.** Trivial edits, test-only changes, and config-only changes are skipped, so ZAR stays quiet when there's nothing to say.
3. **Asks Claude for the smallest possible edit** to your existing Markdown that makes it true again — a minimal unified diff, grounded strictly in the change. It never invents features that aren't in the code.
4. **Delivers the suggestion as a pull request** plus a comment on the source PR, and posts a `zar/docs` commit status. You review and merge like any other change.

```
A PR changes  ──▶  ZAR reads the diff  ──▶  proposes a minimal doc patch  ──▶  you review & merge
   the code         (Claude + tree-sitter)        (a real PR, not a force-push)
```

## What ZAR is not

- **It is not a hosted docs website.** ZAR edits the Markdown in your repo; it does not host or render a site. A publishing platform like [Mintlify](https://mintlify.com) is complementary — ZAR keeps the source correct, the platform publishes it.
- **It is not an autonomous committer.** By default ZAR only comments and opens PRs. Writing directly to your branches requires two explicit opt-ins (see [Auto-commit](/features/auto-commit)).
- **It does not invent documentation.** ZAR edits docs you already have to match code that already exists. It won't fabricate APIs, and it won't write a whole docs site from scratch.

## Why PR-native matters

Most docs tooling lives in a separate place you have to remember to visit. ZAR lives inside the review you're already doing:

- Suggestions appear as **PR comments and a docs PR**, so they're reviewed with the code that caused them.
- An optional **[CI gate](/features/ci-gate)** can block a merge when docs look stale, exactly like a failing test.
- You drive it with **[plain-English commands](/features/commands)** in a comment — no separate dashboard trip required for everyday actions.

## Who ZAR is for

- **Teams whose docs lag behind code** and want a repeatable habit instead of good intentions.
- **Maintainers** who review a lot of PRs and want docs accuracy enforced at the same gate as tests.
- **Anyone publishing docs** (including Nextra/MDX, including multilingual sites) who wants the source Markdown to stay honest.

## Next steps

- **[Quickstart](/getting-started/quickstart)** — install ZAR and see your first suggestion in a few minutes.
- **[How ZAR works](/concepts/how-it-works)** — the full pipeline, end to end.
- **[Safety & guarantees](/concepts/safety-and-guarantees)** — exactly what ZAR will and won't do to your repo.
