---
title: Plans
description: ZAR's plan tiers, what each includes, and how access is determined.
---

# Plans

ZAR is a subscription product. A paid plan (or complimentary access granted by the ZAR team) is required before ZAR will **open documentation PRs**. While you evaluate, ZAR can still run analysis and post commit statuses, but PR creation is gated by your plan.

There is no perpetual free tier in the product today; pricing and any trial are presented on the [dashboard](https://app.zarlabs.tech) and the GitHub Marketplace listing.

## Tiers

| Capability | Pro | Team | Enterprise |
|--|--|--|--|
| Repositories | 10 | Unlimited | Unlimited |
| Doc commits / month | 500 | Unlimited | Unlimited |
| Private repositories | ✓ | ✓ | ✓ |
| Style guide | ✓ | ✓ | ✓ |
| [CI gate](/features/ci-gate) | — | ✓ | ✓ |
| [Cross-repo docs](/features/cross-repo-docs) | — | ✓ | ✓ |
| MCP server | — | Soon | Soon |

Pro is the entry tier for individuals and small teams. Team unlocks the org-wide controls — the merge gate and cross-repo docs; an MCP server for Cursor and Claude Desktop is coming soon. Enterprise carries the same capabilities as Team with arrangements suited to larger organizations.

## How access is decided

ZAR resolves access per team at PR-creation time:

- **Active subscription** — a Pro, Team, or Enterprise plan in good standing (billed through [Polar](https://polar.sh)). ZAR operates normally within the tier's limits.
- **Complimentary ("comped") access** — granted manually by the ZAR team; behaves like Team and doesn't expire.
- **No access** — analysis and statuses may run, but ZAR won't open documentation PRs until a plan is active.

When access is missing, ZAR records "Subscription required" on the run and posts a `failure` commit status rather than silently doing nothing — so it's clear *why* no PR appeared. Check the [run view](/features/dashboard#run-view) if you're unsure.

## Pricing

Current prices live on the [dashboard](https://app.zarlabs.tech) and the GitHub Marketplace listing, where billing is configured. This page describes what each tier *includes*; for the number you'll pay, see those sources.

## Next steps

- **[CI gate](/features/ci-gate)** and **[Cross-repo docs](/features/cross-repo-docs)** — the Team-tier features.
- **[Dashboard](/features/dashboard)** — manage your plan and see access state.
- **[FAQ](/faq)** — common questions about access and limits.
