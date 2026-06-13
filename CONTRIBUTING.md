# Contributing to the ZAR docs

These docs describe ZAR, a GitHub App that keeps repository documentation accurate as code changes. This repo ([`crubn/zar-docs`](https://github.com/crubn/zar-docs)) holds the documentation; the ZAR product code lives in a separate private repository.

## Ways to help

- **Fix something wrong.** If a flag, default, command, or behavior here doesn't match what ZAR actually does, open a PR or an issue. Accuracy is the whole point.
- **Improve clarity.** Shorter sentences, better examples, a diagram where prose struggles.
- **Fill a gap.** If you hit a question these docs didn't answer, that's a missing page.

## How the docs are organized

We loosely follow the [Diátaxis](https://diataxis.fr) framework:

- **Getting started** — learning-oriented: install ZAR and see it work.
- **Concepts** — understanding-oriented: how and why ZAR behaves the way it does.
- **Configuration / Features** — task-oriented: do a specific thing.
- **Reference** — information-oriented: complete tables of settings, commands, env vars.

Each page is a Markdown file with a small YAML frontmatter block (`title`, `description`) so it renders cleanly both on GitHub and in [Mintlify](https://mintlify.com). The site navigation is defined in [`docs.json`](docs.json) — if you add a page, add it there too.

## Local preview (Mintlify)

```bash
npm i -g mint
mint dev
```

This serves the docs at `http://localhost:3000` using `docs.json`.

## Style

- Write for a smart person who hasn't seen the code.
- Prefer concrete nouns and active voice. "ZAR opens a PR", not "a PR is opened".
- Every claim about behavior should match the code. When in doubt, open an issue to confirm the behavior.
- Keep examples copy-pasteable.

## Reporting product bugs

This repo is for documentation. For bugs in ZAR itself, open an issue in [`crubn/zar-docs`](https://github.com/crubn/zar-docs/issues).
