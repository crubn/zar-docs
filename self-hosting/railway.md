---
title: Deploy on Railway
description: Run ZAR on Railway with managed Postgres and Redis.
---

# Deploy on Railway

[Railway](https://railway.app) is a good fit for ZAR: it provisions Postgres and Redis, injects their connection strings, and gives you a public HTTPS domain for the webhook. The repo includes `railway.toml`, `nixpacks.toml`, and a `start.py` entrypoint that reads Railway's `$PORT`.

## 1. Create the service

1. Create a new Railway project from your copy of the ZAR source (it includes `railway.toml`, `nixpacks.toml`, and `start.py`).
2. Railway detects the Dockerfile / Nixpacks build and starts a web service.

## 2. Add Postgres and Redis

Add the **PostgreSQL** and **Redis** plugins to the project. Railway injects:

- `DATABASE_URL` (a `postgres://…` URL) — ZAR uses the async Postgres driver.
- `REDIS_URL`.

You don't need to set these by hand once the plugins are attached.

## 3. Set environment variables

In the service **Variables**, set at least:

```bash
GITHUB_APP_ID=...
GITHUB_WEBHOOK_SECRET=...
GITHUB_PRIVATE_KEY=-----BEGIN RSA PRIVATE KEY-----\n...    # paste the full PEM
ANTHROPIC_API_KEY=sk-ant-...
APP_ENV=production
SESSION_SECRET=...        # python -c "import secrets; print(secrets.token_hex(32))"
```

On Railway, prefer `GITHUB_PRIVATE_KEY` (the inline PEM) over `GITHUB_PRIVATE_KEY_PATH` — there's no `.pem` file to mount. See [Environment variables](/reference/environment-variables) for the rest (limits, OAuth, SMTP, billing).

## 4. Deploy and get the domain

Deploy. Once it's live, open the service **Settings → Networking** and generate a public domain (e.g. `https://your-app.up.railway.app`).

## 5. Point GitHub at the webhook

Set your GitHub App's **Webhook URL** to:

```
https://your-app.up.railway.app/github/webhook
```

(Note the `/github` prefix.)

## 6. Verify

- Health check: `GET https://your-app.up.railway.app/healthz` → `{"ok": true}`.
- Railway's healthcheck should pass against `/healthz`.
- Install the App on a repo and open a test PR, or use the dashboard's **Send test webhook**.

## Notes

- The start command resolves to `uvicorn app.main:app` bound to Railway's `$PORT`.
- Use Postgres (auto-injected) rather than SQLite for anything beyond a quick trial.
- If you change the domain later, update the GitHub App's Webhook URL to match.

## Next steps

- **[Environment variables](/reference/environment-variables)** — full configuration.
- **[Docker](/self-hosting/docker)** — if you'd rather run containers elsewhere.
- **[Troubleshooting](/troubleshooting)** — webhook delivery problems.
