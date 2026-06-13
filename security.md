---
title: Security
description: How ZAR protects your repository, how to report a vulnerability, and hardening guidance for operators.
---

# Security

> This page describes ZAR's security posture and how to report issues. If you run a self-hosted instance, treat the operator sections as a checklist and adapt the contact details to your deployment.

## How ZAR protects you

- **Signed webhooks.** Every incoming webhook is verified with an HMAC-SHA256 signature (`X-Hub-Signature-256`) against your webhook secret. Unverified events are rejected and never processed.
- **Least-privilege permissions.** ZAR requests only the GitHub App scopes it needs. Write to Contents is exercised only when you enable [auto-commit](features/auto-commit.md). See [Permissions & events](reference/permissions-and-events.md).
- **No writes by default.** Direct commits require two independent opt-ins. See [Safety & guarantees](concepts/safety-and-guarantees.md#2-zar-never-writes-by-default).
- **Bounded data to third parties.** Only the code excerpts and docs needed to generate a suggestion are sent to Anthropic's Claude. See [Privacy](privacy.md).

## Reporting a vulnerability

Please report security issues privately rather than opening a public issue with details.

- **Email:** `security@docagent.dev` (self-hosted operators: replace with your own security contact).
- If you must use GitHub, open an issue **without sensitive details** and ask for a private channel.
- We aim to acknowledge reports within **72 hours**.

Please don't disclose publicly until a fix is available.

## Hardening (for operators)

If you self-host ZAR:

- **Rotate the webhook secret** (`GITHUB_WEBHOOK_SECRET`) if you suspect exposure, and update it in both the GitHub App and your environment.
- **Serve over HTTPS only**, and make sure your proxy doesn't alter the request body (it breaks signature verification).
- **Restrict dashboard access** — set `DOCAGENT_DASHBOARD_PASSWORD`, or front it with your own auth/network controls.
- **Generate a strong `SESSION_SECRET`** in production (`python -c "import secrets; print(secrets.token_hex(32))"`).
- **Keep `DOCAGENT_ALLOW_DEV_TOOLS` off** in production — it exposes developer-only admin helpers.
- **Use least-privilege GitHub App permissions** — the manifest in the product repo is a good baseline.

## Never commit secrets

Do not commit `.env`, the GitHub App private key (`.pem`), or the database (`docagent.sqlite3`). Use environment variables and secret stores. See [Environment variables](reference/environment-variables.md).

## Related

- [Privacy](privacy.md) — what data ZAR handles.
- [Safety & guarantees](concepts/safety-and-guarantees.md) — the behavioral guarantees.
- [Self-hosting](self-hosting/overview.md) — secure deployment.
