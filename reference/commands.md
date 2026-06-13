---
title: Commands reference
description: The complete syntax for ZAR's PR/issue comment commands, including aliases and accepted forms.
---

# Commands reference

The full grammar for ZAR's comment commands. For a friendlier walkthrough, see [Commands](../features/commands.md).

## Invocation

A command is a comment that begins with one of these tokens (case-insensitive), followed by the command text:

```text
@docagent <command>
/docagent <command>
@zar <command>
/zar <command>
```

All four prefixes are equivalent.

## Permission

The author must have **write** access. `OWNER`, `MEMBER`, and `COLLABORATOR` are accepted automatically; others are checked for `write`, `maintain`, or `admin` permission via GitHub. Insufficient permission gets a refusal reply.

## Commands

| Action | Accepted forms | Context | Notes |
|--|--|--|--|
| **Help** | `help`, `commands` | PR or issue | Lists commands with examples. |
| **Status** | `status`, `settings` | PR or issue | Shows CI gate, auto-commit, `use_updates_branch`, and a style-guide preview. |
| **Set style** | `style "<text>"`, `style '<text>'`, `style <text>` | PR or issue | Sets the [style guide](../configuration/style-guide.md). Quoted or unquoted. |
| **CI gate on/off** | `enable ci-gate`, `enable ci gate`, `disable ci-gate`, `disable ci gate` | PR or issue | Toggles the [CI gate](../features/ci-gate.md). Dash or space. |
| **Auto-commit on/off** | `enable auto-commit`, `enable auto commit`, `disable auto-commit`, `disable auto commit` | PR or issue | Toggles [auto-commit](../features/auto-commit.md). Still requires the `DOCAGENT_WRITE_COMMITS` env var to actually commit. |
| **Update now** | `update docs now`, `update docs`, `update documentation now`, `update` | **PR only** | Forces a docs analysis on the current PR, ignoring trigger mode. |

## Behavior notes

- **Parsing.** ZAR first matches commands with a fast deterministic parser; if that doesn't match, it falls back to interpreting the comment with Claude. Plain forms above always match the fast path.
- **Replies.** ZAR responds in a threaded reply, mentioning the author, confirming the change or explaining why it couldn't act (e.g. "didn't understand the command", or "update" used outside a PR).
- **Quotes for `style`.** Both `"…"` and `'…'` work; unquoted text is taken up to the end of the command. Use quotes when the style text contains punctuation.
- **Immediate effect.** Toggles and style changes apply right away; there's no separate confirmation step.

## Examples

```text
@docagent help
@zar status
@docagent style "US English. Short sentences. Params in tables. One code example per endpoint."
/docagent enable ci-gate
@docagent disable auto-commit
@zar update docs now
```

## Related

- [Commands](../features/commands.md) — narrative guide.
- [CI gate](../features/ci-gate.md) · [Auto-commit](../features/auto-commit.md) · [Style guide](../configuration/style-guide.md) — what the toggles control.
