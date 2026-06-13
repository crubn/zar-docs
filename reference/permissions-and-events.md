---
title: Permissions & events
description: The GitHub App permissions ZAR requests, the webhook events it subscribes to, and its HTTP endpoints.
---

# Permissions & events

ZAR requests the least privilege it needs to read diffs and post reviewable feedback. This page is the exact list, plus the HTTP endpoints a self-hosted instance exposes.

## GitHub App permissions

| Permission | Level | Why |
|--|--|--|
| Metadata | Read | Required for every GitHub App. |
| Contents | Read & write | Read code diffs and doc files; **write** is used only when [auto-commit](../features/auto-commit.md) is enabled. |
| Pull requests | Read & write | List changed files; post review comments and docs PRs. |
| Issues | Read & write | Reply to `@docagent` [commands](../features/commands.md); post the welcome issue. |
| Checks | Read & write | Create the `docagent/ci-gate` check run for the [CI gate](../features/ci-gate.md). |
| Commit statuses | Read & write | Post the `zar/docs` status on analyzed commits. |

> `Contents: write` is requested so that auto-commit can work when you turn it on. With auto-commit off (the default), ZAR reads contents but only ever *writes* via pull requests. See [Safety & guarantees](../concepts/safety-and-guarantees.md#2-zar-never-writes-by-default).

## Webhook events

| Event | Trigger | What ZAR does |
|--|--|--|
| `push` | Commits pushed to the watched branch | Analyze the diff; propose docs. |
| `pull_request` | `opened`, `synchronize`, `ready_for_review`, merge | Analyze PR changes; comment / open docs PR; run the CI gate if enabled. |
| `issue_comment` | `created` | Run `@docagent` / `/docagent` commands. |
| `installation` | App installed / removed / suspended | Onboarding and lifecycle. |
| `installation_repositories` | Repo added to an existing install | Onboarding for the new repo. |

`installation` and `installation_repositories` are delivered to every GitHub App automatically — there's no separate subscription checkbox. Events other than the above are acknowledged and ignored.

## HTTP endpoints (self-hosted)

ZAR is a FastAPI app. The endpoints you'll care about:

| Method & path | Purpose |
|--|--|
| `POST /github/webhook` | The webhook URL GitHub must call. Set your GitHub App's Webhook URL to `https://YOUR_DOMAIN/github/webhook`. |
| `GET /health` | JSON health/status (service, version, environment, write-commits flag). |
| `GET /healthz` | Minimal liveness probe (`{"ok": true}`). |
| `GET /dashboard` | The dashboard UI (optionally password-protected). |
| `GET /auth/github`, `GET /auth/callback` | GitHub OAuth login for the dashboard. |
| `GET /setup`, `GET /setup/installed` | GitHub App install wizard. |
| `POST /webhooks/polar` | Polar billing webhook (only if billing is configured). |

> The webhook router is mounted under `/github`, so the full public path is `/github/webhook` — a common mistake is to configure just `/webhook`.

Admin and MCP routes exist behind their own secrets (`DOCAGENT_ADMIN_TOKEN`, `ADMIN_SECRET`, `DOCAGENT_MCP_SECRET`); see [Environment variables](environment-variables.md).

## Related

- [Installation](../getting-started/installation.md) — installing with these permissions.
- [Self-hosting overview](../self-hosting/overview.md) — registering your own App.
- [Security](../security.md) — webhook verification and hardening.
