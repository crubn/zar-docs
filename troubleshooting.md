---
title: Troubleshooting
description: Common ZAR symptoms and how to fix them.
---

# Troubleshooting

Most problems fall into a handful of buckets. Find your symptom below. When in doubt, open the [run view](/features/dashboard#run-view) for the PR or push — its timeline usually shows exactly which step stopped.

## "Validate GitHub App" fails

**Likely cause:** the App ID, private key, or webhook secret don't line up.

- Confirm the **App ID** matches your configuration.
- Confirm the **private key** (`.pem`) is the one for *this* App and isn't truncated. On cloud hosts, paste the full PEM into `GITHUB_PRIVATE_KEY` (including the `BEGIN`/`END` lines).
- Confirm the **webhook secret** matches exactly — no trailing spaces or newlines.

## Webhooks aren't arriving (no runs after a push/PR)

**Likely cause:** GitHub can't reach your instance, or the URL is wrong.

- In **GitHub → your App → Advanced → Recent Deliveries**, check that deliveries return `2xx`.
- The Webhook URL must end with **`/github/webhook`** (note the `/github` prefix). A bare `/webhook` is the most common mistake.
- Make sure the instance is publicly reachable (DNS, TLS, firewall).
- Ensure any proxy in front of ZAR **doesn't modify the request body** — that breaks HMAC verification and ZAR will reject the event with `401`.

## "Send test webhook" says `local` instead of `queued`

**Not a failure.** `queued` means a background worker picked up the event; `local` means the API processed it inline. Both are healthy. If you expect `queued`, make sure Redis and your worker process are running.

## No PR comment from ZAR

Work through these in order:

- **Is the App installed on *this* repo?** Org-level install isn't enough; the specific repository must be selected.
- **Did the PR change real behavior?** Lock-file-only, test-only, and config-only diffs are skipped by design. Try a change that adds a public function, endpoint, or config flag. See [significance](/configuration/trigger-modes#the-significance-filter).
- **Permissions present?** Pull requests (R/W), Issues (R/W), Contents (R/W), Checks (R/W), Metadata (R). See [Permissions & events](/reference/permissions-and-events).
- **Is `ANTHROPIC_API_KEY` set?** (Self-hosted.) Without it, ZAR can't generate suggestions.
- **Rate limited?** ZAR caps analyses per PR per hour. The run timeline will show `rate_limit.blocked`.
- **Trigger mode batching?** On `daily` or `on_significant_change`, a change may be deferred. Force it with `@docagent update docs now`.
- **Subscription required?** If the run summary says "Subscription required", see [Plans](/plans).

## The CI gate doesn't block a merge

- The gate is **off by default** — enable it (`@docagent enable ci-gate`).
- **Dry-run** never blocks. Turn dry-run off for the repo.
- For hard enforcement, add the `docagent/ci-gate` check to **branch protection**. See [CI gate](/features/ci-gate).

## Auto-commit doesn't commit

Auto-commit needs **both** locks:

1. the repo setting `auto_commit_enabled` is on, **and**
2. the operator set `DOCAGENT_WRITE_COMMITS`.

If either is missing, ZAR opens a PR instead. Also check the repo isn't in **dry-run** (which forbids commits). See [Auto-commit](/features/auto-commit).

## ZAR's edit looks wrong or off-voice

- Set a [style guide](/configuration/style-guide) so edits match your conventions, then push again.
- Remember ZAR only edits docs to match real code changes — if it's editing the "wrong" file, check whether the relevant doc actually exists for it to update.

## "Database is locked" or slow runs

You're likely on SQLite under concurrency. Switch to **Postgres** (`DATABASE_URL=postgresql+asyncpg://…`) for production. SQLite is fine for local testing only. See [Self-hosting](/self-hosting/overview#database).

## Duplicate docs PRs

ZAR keeps at most one open docs PR per base branch on a single instance. If you see duplicates, you're probably running **multiple replicas** without a shared lock — see the [scaling note](/self-hosting/overview#scaling-note).

## Still stuck?

- Open the [run view](/features/dashboard#run-view) and read the timeline and `error` field.
- Turn on **debug mode** for the repo for verbose logs and an extra "Debug Info" comment.
- For product bugs, open an issue in [crubn/zar-docs](https://github.com/crubn/zar-docs/issues).
