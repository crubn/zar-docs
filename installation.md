---
title: Installation
description: Install the ZAR GitHub App on your repositories, the permissions it requests, and what happens right after install.
---

# Installation

ZAR runs as a **GitHub App**. There are two ways to install it:

- **Hosted** — install from the dashboard at [app.zarlabs.tech](https://app.zarlabs.tech) (or the GitHub Marketplace listing when available). This is the path most teams want.
- **Self-hosted** — run your own instance and register your own GitHub App. See [Self-hosting](/self-hosting/overview).

This page covers the hosted install and the permissions ZAR asks for in either case.

## Hosted install

1. Go to **[app.zarlabs.tech](https://app.zarlabs.tech)** and sign in with GitHub.
2. Click **Connect a repo**.
3. On GitHub's installation screen, choose the owner, select **Only select repositories**, and pick the repos you want ZAR to watch.
4. Approve the requested [permissions and events](#permissions-and-events).

You can add or remove repositories any time from **GitHub → Settings → Applications → Installed GitHub Apps**, or from the ZAR dashboard.

## Permissions and events

ZAR requests the **least privilege** it needs to read diffs and post reviewable feedback.

| Permission | Level | Why ZAR needs it |
|--|--|--|
| Metadata | Read | Required for every GitHub App. |
| Contents | Read & write | Read code diffs and doc files; write doc updates **only** if you enable auto-commit. |
| Pull requests | Read & write | List changed files and post review comments / docs PRs. |
| Issues | Read & write | Reply to `@docagent` commands and post the welcome issue. |
| Checks | Read & write | Create the optional `docagent/ci-gate` check run. |
| Commit statuses | Read & write | Post the `zar/docs` status on analyzed commits. |

Webhook events ZAR subscribes to:

| Event | Used for |
|--|--|
| `push` | Analyze commits pushed to the watched branch. |
| `pull_request` | Analyze PRs (`opened`, `synchronize`, `ready_for_review`, and merges). |
| `issue_comment` | Run `@docagent` / `/docagent` commands. |
| `installation` | Onboarding when ZAR is installed or removed. |
| `installation_repositories` | Onboarding when a repo is added to an existing install. |

> `installation` and `installation_repositories` are delivered to every GitHub App by default — there's no separate checkbox for them.

Full detail, including the exact `Contents: write` rationale: [Permissions & events](/reference/permissions-and-events).

## What happens right after install

On a fresh installation ZAR does a little onboarding so you're not staring at a blank screen:

- **Welcome issue.** ZAR opens an issue titled "👋 ZAR is ready" with a short orientation and links to the dashboard.
- **Welcome email.** If the operator has email configured, the installer gets a short "ZAR is installed" message (with different copy depending on whether your team has complimentary access).
- **Welcome PR (first repo only).** A few seconds after install, ZAR opens a demo PR on a branch named `zar/welcome` that adds a small `.zar/welcome.md` file explaining how it works. This is a one-time illustration — merge it or close it, your choice. It's skipped if your repo already has ZAR activity.

These steps are **idempotent**: reinstalling or adding more repos won't spam you with duplicates.

## Choosing what ZAR watches

By default ZAR watches your repository's default branch and behaves conservatively (no auto-commit, no CI gate). Before turning it loose on everything, you'll usually want to:

1. Set a [trigger mode](/configuration/trigger-modes) (default: `on_significant_change`).
2. Optionally add a [`.zar.yml`](/configuration/zar-yml) to version your settings in the repo.
3. Optionally set a [style guide](/configuration/style-guide) so edits match your voice.

## Next steps

- **[Quickstart](/quickstart)** — open your first real PR and watch ZAR respond.
- **[Configuration overview](/configuration/overview)** — where settings live and how they combine.
- **[Self-hosting](/self-hosting/overview)** — run ZAR on your own infrastructure.
