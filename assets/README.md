# Assets

Shared images for the docs site and `README.md`.

## Brand

| File | Used by |
|--|--|
| `banner.svg` | `README.md` hero |
| `favicon.svg` | `docs.json` favicon |
| `logo/light.svg` | `docs.json` navbar logo (light mode) |
| `logo/dark.svg` | `docs.json` navbar logo (dark mode) |

All vector (SVG) — crisp at any size and editable by hand.

## Screenshots (currently illustrative mockups)

These are **SVG mockups** standing in until real product screenshots are added:

| File | Used by | Shows |
|--|--|--|
| `pr-comment.svg` | `features/pr-feedback.md` | ZAR's comment on a PR |
| `dashboard.svg` | `features/dashboard.md` | the run-view timeline |
| `ci-gate-check.svg` | `features/ci-gate.md` | a blocked CI-gate check |

### Replacing a mockup with a real screenshot

1. Capture the real screenshot and save it here (e.g. `pr-comment.png`).
2. Update the reference in the corresponding page from `/assets/pr-comment.svg` to `/assets/pr-comment.png`.
3. Remove the `*Illustrative mockup.*` caption.
4. Run `mint broken-links` to confirm nothing broke.
