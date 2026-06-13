---
title: Quickstart
description: Install ZAR on one repository and get your first documentation suggestion in about five minutes.
---

# Quickstart

This guide takes you from zero to your first ZAR documentation suggestion using the hosted app at **[app.zarlabs.tech](https://app.zarlabs.tech)**. No infrastructure required.

> Running your own instance instead? Start with [Self-hosting](../self-hosting/overview.md), then come back here for the "open a real PR" steps.

## What you'll need

- A GitHub account with admin access to at least one repository.
- A repository with some Markdown docs (`README.md` or files under `docs/`). ZAR edits docs that already exist.
- An active ZAR plan or complimentary access. ZAR analyzes and posts commit statuses while you evaluate, but a plan is required before it opens documentation PRs. See [Plans](../plans.md).

## Step 1 — Sign in

Go to **[app.zarlabs.tech](https://app.zarlabs.tech)** and **Sign in with GitHub**. Authorize the dashboard to read which repositories you can install apps on.

You'll land on the dashboard with a single call to action: **Connect a repo**.

## Step 2 — Connect one repository

Click **Connect a repo**. GitHub's app installation screen opens.

1. Choose the account or organization that owns the repo.
2. Select **Only select repositories** and pick **one** repo to start.
3. Confirm the install.

GitHub will ask you to approve ZAR's [permissions](../reference/permissions-and-events.md) (read code, write PR/issue comments, write commit statuses). Approve them.

Back on the dashboard, the repository now shows as **connected**.

> On a fresh install, ZAR also opens a small **welcome PR** (`zar/welcome`) on your first repository that explains how it works. Merging it is optional — it's there to show you the shape of a ZAR PR. See [Onboarding](installation.md#what-happens-right-after-install).

## Step 3 — (Optional) Validate the connection

In the dashboard's **Advanced** section you can:

- **Validate GitHub App** — confirms your App ID, private key, and webhook secret line up. Expect an `OK`.
- **Send test webhook** — pushes a synthetic event. Expect a new entry in **Activity**, marked either `queued` (a background worker picked it up) or `local` (processed inline). Both are healthy.

If either fails, jump to [Troubleshooting](../troubleshooting.md).

## Step 4 — Open a pull request that changes real behavior

ZAR responds to changes that affect what your docs claim. Good triggers:

- Add a new configuration flag or environment variable.
- Add or rename a public function, class, or API endpoint.
- Change a default value or a documented behavior.

Open a PR with a change like that against your default branch.

## Step 5 — Watch ZAR respond

Within a short time of opening the PR, ZAR will:

- Post a **`zar/docs` commit status** on the PR's head commit (`success`, or `pending` if docs need an update).
- If the docs need changes, **comment on the PR** with the proposed Markdown edits and **open a docs PR** containing the patch.
- If the docs already match the change, mark the status green and stay quiet.

Open the docs PR, review the diff, and merge it if it's right. That's the full loop.

## Verify it worked

You've succeeded when you can see, for your test PR:

- A `zar/docs` entry in the PR's checks/statuses, **and**
- Either a ZAR comment with a proposed doc edit, or a clear "docs look good" signal, **and**
- A corresponding **run** in the dashboard's **Activity** view (click it for the step-by-step timeline).

## If nothing happened

The most common causes:

- The PR only touched lock files, tests, or config — ZAR skips those by design. Try a change that adds a public symbol.
- The app is installed on the org but not on **this** repo.
- You're on the default trigger mode (`on_significant_change`) and the change wasn't significant enough. See [Trigger modes](../configuration/trigger-modes.md).

Full symptom-to-fix table: [Troubleshooting](../troubleshooting.md).

## Next steps

- Set a writing style so edits match your voice: **[Style guide](../configuration/style-guide.md)**.
- Turn ZAR into a merge gate: **[CI gate](../features/ci-gate.md)**.
- Let ZAR commit fixes for you: **[Auto-commit](../features/auto-commit.md)**.
- Tune when ZAR speaks up: **[Trigger modes](../configuration/trigger-modes.md)**.
