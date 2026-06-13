---
title: Commands
description: Drive ZAR from a pull request or issue comment with plain-English commands.
---

# Commands

You can control ZAR without leaving GitHub by mentioning it in a pull request or issue comment. Commands are the fastest way to set a style, toggle a feature, or force a docs update.

## Triggers

Start a comment with any of these (case-insensitive):

```text
@docagent …
/docagent …
@zar …
/zar …
```

All four are equivalent. The rest of the comment is the command.

## Who can run them

The comment author needs **write** access to the repository. Owners, members, and collaborators are recognized automatically; for anyone else ZAR checks GitHub for `write`, `maintain`, or `admin` permission. Without it, ZAR replies that the command was refused. This keeps configuration changes in the hands of people who can already change the repo.

## Available commands

| Command | What it does |
|--|--|
| `help` | List the commands with examples. |
| `status` | Show current CI gate, auto-commit, and style-guide settings. |
| `style "<text>"` | Set the documentation [style guide](/configuration/style-guide). |
| `enable ci-gate` / `disable ci-gate` | Turn the [CI gate](/features/ci-gate) on/off. |
| `enable auto-commit` / `disable auto-commit` | Turn [auto-commit](/features/auto-commit) on/off. |
| `update docs now` | Force a docs analysis on the current PR, ignoring trigger mode. |

`ci-gate` / `ci gate` and `auto-commit` / `auto commit` are both accepted (dash or space).

## Examples

```text
@docagent help
```

```text
@docagent status
```

```text
@docagent style "Write like Stripe: short sentences, params in tables, one code example per endpoint."
```

```text
@docagent enable ci-gate
```

```text
@zar update docs now
```

## Notes

- **`style`** accepts quoted (`"…"` or `'…'`) or unquoted text. Quotes are clearest when your style includes punctuation.
- **`update docs now`** only makes sense on a **pull request**; on a plain issue there's no diff to analyze. Variants like `update`, `update docs`, and `update documentation now` all work.
- ZAR replies in a threaded comment confirming what it did (or explaining why it couldn't).
- Commands change settings immediately; there's no separate apply step.

## Full syntax reference

For the exact accepted forms, aliases, and edge cases, see the [Commands reference](/reference/commands).

## Next steps

- **[Style guide](/configuration/style-guide)** — what to pass to `style`.
- **[CI gate](/features/ci-gate)** and **[Auto-commit](/features/auto-commit)** — what the toggles do.
- **[Commands reference](/reference/commands)** — the complete table.
