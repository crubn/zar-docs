---
title: FAQ
description: Frequently asked questions about ZAR.
---

# FAQ

## General

**What is ZAR in one sentence?**
A GitHub App that reads your code diffs and proposes minimal edits to your Markdown docs so they stay accurate as the code changes.

**Is ZAR a docs hosting platform like Mintlify?**
No. ZAR keeps the *source* Markdown correct; a platform like Mintlify *publishes* it. They're complementary — in fact these docs are written to migrate cleanly to Mintlify.

**Does ZAR write documentation from scratch?**
No. It edits docs that already exist to match real code changes. It won't scaffold a new docs site or invent APIs.

## Behavior

**Will ZAR edit my files without asking?**
No. By default it only comments and opens pull requests. Direct commits require both the `auto_commit_enabled` setting **and** the operator's `DOCAGENT_WRITE_COMMITS` env var. See [Auto-commit](features/auto-commit.md).

**Will it comment on every commit?**
No. The default trigger mode is `on_significant_change`, and trivial, test-only, and config-only changes are skipped. See [Trigger modes](configuration/trigger-modes.md).

**Why didn't ZAR say anything on my PR?**
Usually because the change wasn't doc-worthy (tests/config/trivial), the docs already matched, the change is being batched, or a rate limit was hit. The [run view](features/dashboard.md#run-view) shows which. See [Troubleshooting](troubleshooting.md#no-pr-comment-from-zar).

**Can it make docs worse?**
ZAR proposes a reviewable PR you approve. It also rejects edits that would break MDX builds or dangle content at end-of-file, and it never invents facts. See [Safety & guarantees](concepts/safety-and-guarantees.md).

## Languages & docs

**Which programming languages does it understand?**
It recognizes Python, TypeScript/TSX, JavaScript/JSX, Go, Java, Kotlin, and Rust as code, with the deepest symbol-level extraction (functions, classes, methods) for Python and TypeScript/TSX. It still reads diffs in any language; symbol awareness is best in those.

**Which doc files does it touch?**
`.md`, `.mdx`, and `.markdown`. It ignores build output and navigation files like `meta.json`.

**Does it support multilingual docs?**
Yes — Nextra-style language variants (e.g. `index.en.mdx`, `index.kk.mdx`). It mirrors an edit across sibling languages and preserves frontmatter and imports. See [MDX & multilingual](features/mdx-and-multilingual.md).

**What language does it write in?**
The same language each doc is already in. ZAR detects the language and won't switch it. A [style guide](configuration/style-guide.md) tunes the wording within that language.

## Setup & hosting

**Do I have to self-host?**
No. Use the hosted app at [app.zarlabs.tech](https://app.zarlabs.tech). Self-hosting is available if you want full control. See [Self-hosting](self-hosting/overview.md).

**What permissions does it need?**
Metadata (read), Contents (read & write), Pull requests, Issues, Checks, and Commit statuses (read & write). Write to Contents is only used if you enable auto-commit. See [Permissions & events](reference/permissions-and-events.md).

**Is there a CI gate?**
Yes, optional. It blocks merges when docs look stale via a `docagent/ci-gate` check. Available on Team/Enterprise plans. See [CI gate](features/ci-gate.md).

## Plans

**Is there a free tier?**
A plan (or complimentary access) is required before ZAR opens documentation PRs. See [Plans](plans.md).

**How is access decided?**
By your team's subscription state — active paid plan, complimentary access, or none. See [Plans](plans.md#how-access-is-decided).

## Privacy

**What data leaves my repo?**
Bounded code excerpts and the relevant docs are sent to Anthropic's Claude to generate suggestions. See [Privacy](privacy.md).

**How do I report a security issue?**
See [Security](security.md).

## Didn't find your answer?

Check [Troubleshooting](troubleshooting.md), or open an issue in [crubn/docagent](https://github.com/crubn/docagent/issues).
