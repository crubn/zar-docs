---
title: How ZAR works
description: The full pipeline from a GitHub webhook to a documentation pull request — and the decisions ZAR makes along the way.
---

# How ZAR works

ZAR is an event-driven pipeline. A GitHub webhook comes in, and a series of gates decides whether — and how — to propose a documentation change. This page walks the whole path so you can predict ZAR's behavior and tune it with confidence.

## The pipeline at a glance

```
GitHub webhook (push / pull_request / issue_comment)
        │
        ▼
1. Verify HMAC signature ───────── fail ──▶ reject (401)
        │ ok
        ▼
2. Respond 202 immediately, process in the background
        │
        ▼
3. Route by event type        issue_comment ──▶ run @docagent command
        │ push / pull_request
        ▼
4. Load repo settings  +  .zar.yml  ──▶ effective config
        │
        ▼
5. Fetch the diff  ──▶  tree-sitter summarizes changed files
        │
        ▼
6. Significance check ─── not significant ──▶ set "zar/docs" ✓, stop
        │ significant
        ▼
7. Trigger policy (mode + batching + confidence)  ── defer ──▶ "pending", stop
        │ proceed
        ▼
8. (PR only) CI gate, if enabled ── blocked ──▶ comment + labels + check, stop
        │
        ▼
9. Claude proposes minimal Markdown patches (grounded in the diff)
        │
        ▼
10. Open / update docs PR  +  comment on source PR  +  set "zar/docs"
        (commit directly only if auto-commit is enabled AND allowed)
```

## Step by step

### 1. Webhook arrives and is verified

GitHub sends a signed `POST` to `/github/webhook`. ZAR verifies the `X-Hub-Signature-256` HMAC against the configured webhook secret. Anything that fails verification is rejected with `401` and never processed. See [Safety & guarantees](/concepts/safety-and-guarantees#1-every-webhook-is-verified).

### 2. Fast acknowledgement, background work

ZAR returns `202 Accepted` right away and does the real work in a background task. GitHub gets a fast response; analysis (which calls Claude) happens off the request path.

### 3. Routing by event type

Only three event types do work:

- **`issue_comment`** (action `created`) → routed to the [command handler](/features/commands). Code analysis does not run here.
- **`push`** → analyze the commits between the `before` and `after` SHAs.
- **`pull_request`** (`opened`, `synchronize`, `ready_for_review`, and merges) → analyze the PR's changed files.

Other events are acknowledged and ignored. Pushes authored by bots (including ZAR itself) are skipped to prevent feedback loops.

### 4. Effective configuration

ZAR loads the repository's settings from its database and merges them with a [`.zar.yml`](/configuration/zar-yml) file from the repo root, if present. Dashboard/database values take precedence over `.zar.yml` where both are set. The result is the *effective config* — trigger mode, watched branch, confidence threshold, style guide, and feature flags — used for the rest of the run. See [Configuration overview](/configuration/overview).

### 5. Fetch and summarize the diff

ZAR fetches the changed files (GitHub's compare endpoint for pushes, the PR files endpoint for PRs) and runs [tree-sitter](https://tree-sitter.github.io/) over the code. tree-sitter extracts **public symbols** — functions, classes, methods — and notes added/removed/renamed ones. Lock files, build output, and minified assets are filtered out.

Code files are recognized by extension across Python, TypeScript/TSX, JavaScript/JSX, Go, Java, Kotlin, and Rust; symbol-level extraction (functions, classes, methods) is deepest for Python and TypeScript/TSX. Documentation files are recognized by extension: `.md`, `.mdx`, `.markdown`.

### 6. Is the change doc-worthy?

ZAR applies a **significance check** so it doesn't nag on noise. A change is treated as significant if it includes any of:

- A new public function, class, method, or API route.
- A deleted or renamed file.
- A signature change (symbols both removed and added in the same file).
- A "meaningful" diff of at least ~10 lines (whitespace-only changes don't count).

A change is **skipped** when it's test-only, config-only, or a trivial diff. When skipped, ZAR simply posts a green `zar/docs` status and stops. Details and tuning: [Trigger modes](/configuration/trigger-modes).

### 7. Trigger policy

Even a significant change might not open a PR *right now*. The [trigger mode](/configuration/trigger-modes) decides:

- `every_commit` — act on every analyzed commit.
- `on_significant_change` *(default)* — act when the change is significant and confident.
- `daily` — batch changes and open a PR once per `batch_hours` window.
- `on_merge` — act only when a PR is merged.

Batching uses a *pending state* so deferred changes accumulate and surface together. A manual `@docagent update docs now` overrides all of this and forces a run.

### 8. CI gate (pull requests only, optional)

If you've enabled the [CI gate](/features/ci-gate), ZAR asks Claude whether the docs are still accurate for this PR. If they're not, ZAR:

- comments on the PR with the proposed edits,
- adds the `docs-needed` and `ci-blocked` labels,
- creates a failing `docagent/ci-gate` check run,

and stops there. In [dry-run](/concepts/safety-and-guarantees#4-dry-run-is-a-hard-guarantee) mode the gate reports but never blocks.

### 9. Claude proposes minimal patches

ZAR builds a prompt containing the repo's relevant docs, the unified diff, and the tree-sitter symbol summary, then asks Claude for a structured response: a list of files to update, each with a **minimal unified diff** and a short explanation. The system prompt constrains Claude hard:

- Only edit when the code change made the docs inaccurate or incomplete.
- Make the smallest surgical edit; never append filler at the end of a file.
- Never invent facts; never document internal/private details.
- Write in the doc's existing language; preserve structure and frontmatter.

If nothing needs to change, the correct answer is an empty list — and ZAR stays quiet.

### 10. Deliver the result

ZAR applies the patches to a working copy and then:

- **Opens or updates a single docs PR** on a `zar-updates/…` branch targeting your base branch. ZAR keeps at most one open docs PR per base, superseding older ones.
- **Comments on the source PR** (when enabled) showing what changed and why.
- **Sets the `zar/docs` commit status** to reflect the outcome.
- **Commits directly only** when you've enabled auto-commit *and* the operator allows writes. See [Auto-commit](/features/auto-commit).

For `.mdx` files, ZAR runs an integrity check and rejects any edit that would strip frontmatter keys or imports — a broken Nextra build is worse than a stale doc. For multilingual sites, it mirrors an edit across sibling language variants. See [MDX & multilingual](/features/mdx-and-multilingual).

## Every run is recorded

Each webhook delivery becomes one **run** in the dashboard, identified by GitHub's delivery ID. A run stores the event, the changed/code/doc files, how many doc changes were suggested versus applied, the feature flags in effect, and a step-by-step timeline. See [Dashboard](/features/dashboard).

## Where to go next

- **[Safety & guarantees](/concepts/safety-and-guarantees)** — the promises this pipeline keeps.
- **[Trigger modes](/configuration/trigger-modes)** — control steps 6 and 7.
- **[CI gate](/features/ci-gate)** — turn step 8 into a merge gate.
