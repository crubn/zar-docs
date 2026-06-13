---
title: Style guide
description: Teach ZAR your documentation voice so its edits read like the rest of your docs.
---

# Style guide

The **style guide** is natural-language guidance ZAR passes to Claude when it writes doc edits. A good one makes ZAR's suggestions read like they were written by your team, not bolted on.

## How to set it

Two equivalent ways:

**In `.zar.yml`** (version-controlled, reviewed in PRs):

```yaml
style_guide: |
  Write like Stripe's API docs: short sentences, second person ("you").
  One runnable code example per endpoint.
  Use tables for parameters with columns: Name, Type, Required, Description.
  Never use marketing adjectives ("powerful", "seamless").
  British spelling.
```

**With a command** (quick, no commit):

```text
@docagent style "Short sentences. Tables for params. A code example per endpoint. No marketing language."
```

See [Commands](../features/commands.md). The dashboard exposes the same field if you prefer a text box.

## What to put in it

Be concrete. ZAR follows specifics far better than vibes.

- **Voice & person** — "second person", "imperative", "no first-person plural".
- **Sentence length & tone** — "short declarative sentences", "no marketing language".
- **Structure** — "every endpoint gets: summary, params table, example, response".
- **Formatting** — "use tables for parameters", "fenced code blocks with language tags".
- **Terminology** — "call it a *workspace*, never a *project*".
- **Spelling/locale** — "US English", "Oxford comma".
- **Examples** — "prefer `curl` examples", "use realistic values, not `foo`/`bar`".

## Good vs. vague

| Vague | Concrete |
|--|--|
| "Make it clear" | "One idea per sentence; max ~20 words." |
| "Be consistent" | "Always call the product *ZAR*; never *docagent* in prose." |
| "Add examples" | "Every config key gets a fenced YAML example with a realistic value." |
| "Professional tone" | "No adjectives like *powerful*, *robust*, *seamless*." |

## What the style guide does not do

- It does not make ZAR **invent** content. ZAR still only edits docs to match real code changes; the style guide shapes *how* it writes, not *whether* it writes.
- It does not change **which** files ZAR touches — that's [trigger modes](trigger-modes.md) and significance.
- It does not override **language detection**. ZAR writes each doc in the language it's already in (see [MDX & multilingual](../features/mdx-and-multilingual.md)); the style guide tunes wording within that language.

## Tips

- Keep it to a handful of strong rules. A wall of text dilutes the signal.
- Mirror the conventions already in your best docs page, so new edits blend in.
- Iterate: open a PR, read ZAR's edit, and add a rule for anything that felt off.

## Next steps

- **[`.zar.yml` reference](zar-yml.md)** — the `style_guide` key.
- **[Commands](../features/commands.md)** — set the style from a PR comment.
- **[PR feedback](../features/pr-feedback.md)** — see the style applied in a real suggestion.
