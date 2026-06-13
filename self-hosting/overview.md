---
title: Self-hosting overview
description: Run your own ZAR instance — prerequisites, the GitHub App you register, and the minimal run command.
---

# Self-hosting overview

ZAR is a FastAPI application you can run anywhere that gives you a public HTTPS URL for webhooks. The hosted app at [app.zarlabs.tech](https://app.zarlabs.tech) is the zero-setup option; self-hosting gives you full control over data and configuration.

## Prerequisites

- **Python 3.12+** (if running from source).
- A **public HTTPS URL** for webhooks — Railway, Render, Fly.io, a VM behind nginx/Caddy, or Kubernetes ingress.
- An **Anthropic API key** (`ANTHROPIC_API_KEY`).
- **GitHub App credentials**: App ID, a private key (`.pem`), and a webhook secret.

## Step 1 — Register a GitHub App

You register your own GitHub App (the hosted ZAR App is separate). The repo ships a manifest to make this one click:

1. Use `github-app-manifest.yml` from the [product repo](https://github.com/crubn/docagent).
2. In GitHub: **Settings → Developer settings → GitHub Apps → New GitHub App → Register from manifest** (or create manually with the same [permissions and events](/reference/permissions-and-events)).
3. Set the **Webhook URL** to `https://YOUR_DOMAIN/github/webhook`.
4. Generate and download a **private key** (`.pem`).
5. Note the **App ID** and set a strong **webhook secret**.

## Step 2 — Configure the environment

At minimum:

```bash
GITHUB_APP_ID=123456
GITHUB_WEBHOOK_SECRET=your-strong-secret
GITHUB_PRIVATE_KEY_PATH=./github-app.private-key.pem   # or GITHUB_PRIVATE_KEY=<PEM contents>
ANTHROPIC_API_KEY=sk-ant-...
APP_ENV=production
```

The full list — limits, OAuth, billing, SMTP — is in [Environment variables](/reference/environment-variables).

## Step 3 — Run it

From source:

```bash
pip install -r requirements.txt
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

For production, run under a process manager with multiple workers behind a TLS-terminating proxy:

```bash
gunicorn app.main:app -k uvicorn.workers.UvicornWorker -w 2 -b 0.0.0.0:8000
```

Or use a platform guide: **[Railway](/self-hosting/railway)** · **[Docker](/self-hosting/docker)**.

## Step 4 — Point GitHub at it and verify

1. Confirm the GitHub App's Webhook URL is `https://YOUR_DOMAIN/github/webhook` (note the `/github` prefix).
2. Hit `GET /healthz` — expect `{"ok": true}`.
3. Install the App on a test repo and open a PR, or use the dashboard's **Send test webhook**.

## Database

- **SQLite** (the default) is fine for local testing and small loads.
- For production or higher concurrency, use **Postgres** via `DATABASE_URL=postgresql+asyncpg://…`. SQLite can hit "database is locked" under concurrent writes.

## Scaling note

ZAR serializes writes per repository with an in-process lock, which prevents duplicate docs PRs on a **single instance**. If you run **multiple replicas**, add a shared lock (database advisory lock or Redis) so two replicas don't process the same repository's events simultaneously. For most teams a single well-sized instance plus Postgres is plenty.

## Keeping secrets safe

Never commit `.env`, the `.pem` private key, or the SQLite database. See [Security](/security).

## Next steps

- **[Railway](/self-hosting/railway)** — managed deploy with Postgres + Redis.
- **[Docker](/self-hosting/docker)** — container and compose.
- **[Environment variables](/reference/environment-variables)** — complete configuration.
