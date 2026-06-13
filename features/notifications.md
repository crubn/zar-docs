---
title: Notifications
description: How ZAR notifies you about documentation PRs and skipped commits, and how to control it.
---

# Notifications

ZAR can email you when it acts (or deliberately doesn't), so you don't have to watch the dashboard. Email is the implemented channel.

## Email notifications

When the operator has SMTP configured (always the case on the hosted app), ZAR sends two kinds of email to the repository owner.

### PR opened

When ZAR opens a documentation PR, you get an email with:

- the repository and PR number,
- the files it proposes to change,
- the confidence score,
- a direct link to review and merge.

### Commit analyzed, no PR needed

When ZAR analyzes a commit but decides no doc update is warranted (the change was below the confidence threshold), it can send a short "no PR needed" note explaining why and linking to where you'd adjust the threshold. This email is **rate-limited to at most once per hour per repository**, so a busy push doesn't flood your inbox.

## Controlling notifications

- Notifications default to **on** for new users.
- Every email includes an unsubscribe / settings link (`app.zarlabs.tech/settings`) where you can turn them off.

## Slack

A `notify_slack` key exists in [`.zar.yml`](../configuration/zar-yml.md) for a Slack incoming-webhook URL. Outbound Slack delivery depends on your instance's operator configuration; on a self-hosted instance, treat it as best-effort and verify it end-to-end before relying on it. Email is the channel ZAR implements directly.

## Self-hosting note

Email requires SMTP environment variables (`SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASSWORD`, `SMTP_FROM`). If they're unset, ZAR simply skips sending — it won't error. See [Environment variables](../reference/environment-variables.md).

## Next steps

- **[Environment variables](../reference/environment-variables.md)** — SMTP configuration.
- **[Dashboard](dashboard.md)** — manage notification settings.
- **[Trigger modes](../configuration/trigger-modes.md)** — the confidence threshold behind "no PR needed".
