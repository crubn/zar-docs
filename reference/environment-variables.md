---
title: Environment variables
description: Every environment variable ZAR reads — required, defaults, and what each one does. For self-hosting.
---

# Environment variables

This reference is for operators running their own ZAR instance. On the hosted app at [app.zarlabs.tech](https://app.zarlabs.tech), these are managed for you.

A minimal instance needs four things: a GitHub App identity, a webhook secret, a private key, and an Anthropic API key.

## Required

| Variable | What it does |
|--|--|
| `GITHUB_APP_ID` | Your GitHub App's numeric ID. |
| `GITHUB_WEBHOOK_SECRET` | Secret used to verify the `X-Hub-Signature-256` HMAC on every webhook. |
| `GITHUB_PRIVATE_KEY` **or** `GITHUB_PRIVATE_KEY_PATH` | The App's private key, as PEM content (`GITHUB_PRIVATE_KEY`, preferred in the cloud) or a path to the `.pem` file (`GITHUB_PRIVATE_KEY_PATH`, handy locally). If both are set, the inline key wins. |
| `ANTHROPIC_API_KEY` | API key for Claude, used to generate doc suggestions. |

## Claude

| Variable | Default | What it does |
|--|--|--|
| `ANTHROPIC_MODEL` | `claude-3-5-sonnet-latest` | Claude model ID. Set to a current model (for example a Sonnet 4.x or Opus 4.x ID) for best results. |

## Write-back

| Variable | Default | What it does |
|--|--|--|
| `DOCAGENT_WRITE_COMMITS` | `true` | Master switch for committing doc updates to GitHub. Auto-commit also requires the per-repo `auto_commit_enabled` setting. Accepts `1/true/yes/on/y` and `0/false/no/off/n`. Set to `false` to globally forbid all writes. See [Auto-commit](/features/auto-commit). |

## Analysis limits & cost control

| Variable | Default | What it does |
|--|--|--|
| `DOCAGENT_MAX_FILES` | `50` | Max code files analyzed per event. |
| `DOCAGENT_MAX_PATCH_CHARS` | `120000` | Max characters of code diff sent to Claude. |
| `DOCAGENT_MAX_DOCS_TOTAL_CHARS` | `120000` | Max total characters across doc files sent to Claude. |
| `DOCAGENT_MAX_DOC_FILE_CHARS` | `30000` | Max characters per individual doc file. |
| `DOCAGENT_MIN_CONFIDENCE` | `0.4` | Floor confidence to trigger an update (distinct from the per-repo `threshold`). |
| `DOCAGENT_MAX_PR_ANALYSES_PER_HOUR` | `2` | Max Claude analyses per PR per hour (rolling window). |

## Infrastructure

| Variable | Default | What it does |
|--|--|--|
| `DATABASE_URL` | `sqlite+aiosqlite:///./docagent.sqlite3` | SQLite (dev) or Postgres (`postgresql+asyncpg://…`) for production. |
| `REDIS_URL` | `redis://localhost:6379/0` | Redis for rate limiting; optional but recommended. |
| `HOST` | `0.0.0.0` | Bind host. |
| `PORT` | `8000` | Bind port. |
| `APP_ENV` | `dev` | `development` / `production` / `dev`. `production` enables JSON logs. |
| `LOG_LEVEL` | `INFO` | `DEBUG` / `INFO` / `WARNING` / `ERROR`. |

## Dashboard, OAuth & admin

| Variable | Default | What it does |
|--|--|--|
| `DOCAGENT_PUBLIC_URL` | — | Public base URL, used in emails and redirects (e.g. `https://app.zarlabs.tech`). |
| `GITHUB_APP_SLUG` | — | App slug for building the `github.com/apps/<slug>` install URL. |
| `DOCAGENT_DASHBOARD_PASSWORD` | — | Optional password gate for `/dashboard` (empty = open). |
| `GITHUB_OAUTH_CLIENT_ID` / `GITHUB_OAUTH_CLIENT_SECRET` | — | GitHub OAuth app credentials for dashboard login. |
| `GITHUB_REDIRECT_URI` | — | OAuth callback URL; must match the GitHub App settings exactly. |
| `SESSION_SECRET` | `change-me-in-production` | Signs session cookies/JWTs. **Generate a strong value in production:** `python -c "import secrets; print(secrets.token_hex(32))"`. |
| `DOCAGENT_ADMIN_TOKEN` | — | Token for `/admin` endpoints. |
| `ADMIN_SECRET` | — | Header secret (`X-Admin-Secret`) for privileged actions. |
| `DOCAGENT_MCP_SECRET` | — | Auth secret for MCP server clients. |
| `DOCAGENT_ALLOW_DEV_TOOLS` | `false` | Enables `/admin/dev/*` helpers (seed demo runs). **Never enable in production.** |

## Billing (Polar)

Only needed if you run paid plans through [Polar](https://polar.sh).

| Variable | What it does |
|--|--|
| `POLAR_ACCESS_TOKEN` | Polar API token. |
| `POLAR_WEBHOOK_SECRET` | Verifies Polar webhooks. |
| `POLAR_ORGANIZATION_ID` | Polar organization ID. |
| `POLAR_PRODUCT_ID_PRO` / `POLAR_PRODUCT_ID_TEAM` | Product IDs mapped to plans. |

## Email (SMTP)

Optional. If unset, ZAR skips sending email rather than erroring. See [Notifications](/features/notifications).

| Variable | Default | What it does |
|--|--|--|
| `SMTP_HOST` | — | SMTP server host (e.g. `smtp.gmail.com`). Empty disables email. |
| `SMTP_PORT` | `587` | SMTP port. |
| `SMTP_USER` / `SMTP_PASSWORD` | — | SMTP credentials. |
| `SMTP_FROM` | `nur@zarlabs.tech` | From address for transactional email. |

## Related

- [Self-hosting overview](/self-hosting/overview) — putting these together.
- [Railway](/self-hosting/railway) · [Docker](/self-hosting/docker) — deploy guides.
- [Settings reference](/reference/settings) — per-repo settings (vs. these operator-level vars).
