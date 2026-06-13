---
title: MDX & multilingual
description: How ZAR handles .mdx / Nextra docs and multilingual sites — including frontmatter safety and language-variant syncing.
---

# MDX & multilingual

ZAR works with more than plain `README.md`. It understands `.mdx` and Nextra-style multilingual docs, and it's careful not to break the things that make those sites build.

## Recognized doc files

ZAR treats these extensions as documentation: **`.md`**, **`.mdx`**, **`.markdown`**. Build output and vendored directories (`node_modules/`, `.next/`, `build/`, …) are ignored. Navigation files like `meta.json` / `meta.*.json` are **not** edited — those are yours to manage.

## MDX integrity: edits that would break the build are rejected

`.mdx` files carry two things a careless edit can destroy: YAML **frontmatter** (which often drives navigation and titles) and **`import`/`export`** statements (which wire up components). After ZAR applies an edit, it checks that:

- every top-level frontmatter key in the original is still present, and
- every `import` / `export` statement is preserved.

If an edit would strip frontmatter keys or drop imports, ZAR **rejects it** rather than shipping a doc that breaks your Nextra build. A stale doc is recoverable; a broken build blocks everyone. ZAR edits the prose, and leaves frontmatter and components intact.

## Multilingual sites

ZAR recognizes Nextra-style language variants by filename, for example:

```
content/
  index.mdx        # locale-less base
  index.en.mdx     # English
  index.kk.mdx     # Kazakh
  index.ru.mdx     # Russian
```

When ZAR edits one variant of a page, it **mirrors the edit across the sibling variants** in their own languages, so your translations don't drift apart. Variant syncing is capped per run to keep changes reviewable.

### Supported locales

ZAR knows a broad set of locale codes, including `en`, `kk` (Kazakh), `ru`, `uk`, `ky`, `uz`, `tr`, `az`, and many European and Asian languages (`de`, `fr`, `es`, `pt`, `it`, `ja`, `ko`, `zh`, `ar`, and more). The exact set is broad enough for typical Nextra sites; if a variant uses a locale ZAR doesn't recognize, it's treated as a standalone file rather than a variant.

### Language detection for locale-less files

For files without an explicit locale in the name, ZAR detects the language from the content (script-based, with Kazakh distinguished from Russian by its unique letters) and writes edits in that same language. It won't switch a Russian doc to English or vice versa.

## Status

MDX and multilingual support is production-ready for Nextra-style projects, with English and Kazakh as well-exercised examples and other locales supported. As with all of ZAR, it edits docs that already exist — it won't scaffold a new localized site for you.

## Tips

- Keep frontmatter keys you care about (like `title`) in the source; ZAR will preserve them, and that's also what drives your nav.
- If you keep navigation in `meta.json`, manage it yourself — ZAR intentionally leaves it alone.
- Pair this with [cross-repo docs](/features/cross-repo-docs) if your MDX site lives in its own repository.

## Next steps

- **[Cross-repo docs](/features/cross-repo-docs)** — for docs-site-in-its-own-repo setups.
- **[How ZAR works](/concepts/how-it-works)** — where the integrity check sits in the pipeline.
- **[Safety & guarantees](/concepts/safety-and-guarantees#7-bad-edits-are-rejected-not-shipped)** — the rejection guarantees.
