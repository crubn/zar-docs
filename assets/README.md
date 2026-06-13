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

## Animated demo

| File | Used by | Shows |
|--|--|--|
| `demo.gif` | `README.md` hero | the end-to-end flow: a PR changes `config.py` (timeout `60s → 300s`), ZAR flags the stale `README.md`, opens docs PR #43 with the minimal `-60s/+300s` edit, and `zar/docs` goes green |
| `demo.html` | source for `demo.gif` | a self-contained, deterministic animation (`window.seek(ms)`) — edit this, not the GIF |

### Regenerating `demo.gif`

`demo.html` exposes `window.TOTAL_MS` and a pure `window.seek(ms)`, so any headless browser can step it frame-by-frame. Capture frames at 12 fps from a `1000×600` viewport, then assemble with ffmpeg:

```bash
ffmpeg -y -framerate 12 -i frames/f%04d.png \
  -vf "fps=12,scale=900:-1:flags=lanczos,split[s0][s1];[s0]palettegen=max_colors=192:stats_mode=full[p];[s1][p]paletteuse=dither=bayer:bayer_scale=3" \
  demo.gif
```

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
