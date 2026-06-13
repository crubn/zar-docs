---
title: Privacy
description: What data ZAR accesses, what it sends to third parties, what it stores, and your choices.
---

# Privacy

> This page summarizes how ZAR handles data. It is informational, not legal advice. Self-hosted operators control their own data and should adapt this to their deployment and jurisdiction.

## What ZAR accesses

To do its job, ZAR processes:

- **Repository metadata** — full name, default branch, visibility.
- **Pull request and issue metadata** — numbers, titles, labels, timestamps.
- **Code change context** — file paths, diffs, and bounded excerpts of file contents.
- **Comments that mention ZAR** — `@docagent` / `/docagent` commands.
- **Operational data** — webhook delivery records and logs.

## What ZAR sends to third parties

- **Anthropic (Claude).** To generate documentation suggestions, ZAR sends the relevant **bounded** code excerpts, the relevant docs, and a symbol summary. The amount is capped by configurable limits (see [Environment variables](/reference/environment-variables)). Review [Anthropic's privacy policy](https://www.anthropic.com/privacy).

ZAR sends only what's needed to produce a suggestion — not your whole repository.

## What ZAR stores

- **Per-repo settings** and onboarding markers.
- **Run records** — one per webhook delivery: event metadata, changed/code/doc file lists, counts, flags, and a step timeline. See [Dashboard](/features/dashboard).

Storage lives in the instance's database (SQLite or Postgres). On a **self-hosted** instance, retention is entirely under the operator's control. On the hosted app, data is retained to operate the service and shown back to you in the dashboard.

## What ZAR does not do

- It does not commit to your repository unless you explicitly enable [auto-commit](/features/auto-commit) (and the operator allows writes).
- It does not act on webhooks that fail signature verification.
- It does not need or request access beyond the [listed permissions](/reference/permissions-and-events).

## Your choices

- **Uninstall the GitHub App** to stop ZAR receiving new events for your repos.
- **Disable features per repo** in the dashboard (dry-run, disable PRs, turn off comments).
- **Turn off email notifications** via the unsubscribe link or dashboard settings.

## Contact

For privacy questions, use GitHub issues for non-sensitive matters, or the contact in [Security](/security) for anything sensitive.

## Related

- [Security](/security) — protection and vulnerability reporting.
- [Safety & guarantees](/concepts/safety-and-guarantees) — behavioral guarantees.
- [Terms](/terms).
