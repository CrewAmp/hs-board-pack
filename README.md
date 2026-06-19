# Riley Board Pack · HockeyStick

The Riley agent's final deliverable in the HockeyStick portal is a 32-page landscape A4 board pack PDF. This repo holds the **canonical source HTML mockup** + **developer specification** that the server-side render pipeline targets.

## Read first

- **[`DEV_REQUIREMENTS.md`](./DEV_REQUIREMENTS.md)** — v4 spec (2026-06-19). Output spec, design system, page-by-page table, static-vs-dynamic, PDF-vs-platform differences, client variability snapshot, build pipeline, acceptance criteria.

## Live preview

| | URL |
|---|---|
| Production deploy (auto-updates) | https://hs-board-pack.pages.dev |
| Latest PDF (v6) | https://hs-board-pack.pages.dev/safetyculture-board-pack-2026-06-19-v6.pdf |
| DEV_REQUIREMENTS as web view | https://hs-board-pack.pages.dev/DEV_REQUIREMENTS.md |

Hosted on Cloudflare Pages (CrewAmp account, project `hs-board-pack`).

## Local render

```bash
# render the current HTML mockup to a PDF locally
chrome --headless=new --no-pdf-header-footer --no-margins \
       --print-to-pdf=out.pdf file:///path/to/index.html
```

Page size is set in CSS: `@page { size: 338.67mm 190.5mm }` (landscape A4).

## Deploy

```bash
npx wrangler pages deploy . --project-name=hs-board-pack
```

## File map

| File | Role |
|---|---|
| `index.html` | Source mockup — single file, inline CSS, 32 `<section class="page">` blocks |
| `DEV_REQUIREMENTS.md` | Build spec — every page mapped to its platform source |
| `safetyculture-board-pack-2026-06-19-v6.pdf` | Latest rendered snapshot (v6, 2026-06-19) |
| `hs-logo-light.png` | HockeyStick reversed logo for cover + dividers (Primary Logo Reverse_RGB) |
| `hs-mark.png` | HockeyStick icon mark for content-page footers (Growth Icon 1 Colour_RGB) |
| `hs-logo.png` / `hs-wordmark.png` | Reserved colour primary logo for light-bg slots (Primary Logo Colour_RGB) |
| `safetyculture-logo.svg` | Example client logo (replace per engagement) |
| `alex-a2-canvas.png` | Snapshot of the Alex A2 ecosystem prioritisation canvas (placeholder; in production, captured server-side per engagement) |
| `canalysis-lifecycle.png` | Canalys lifecycle services diagram (static analyst content) |
| `jay-mcbain.jpg` | Jay McBain attribution photo (static analyst content) |

## Renumber pages

Any time you add / hide / move a page, run a renumber pass to keep `NN of TOTAL` sequential:

```python
import re, pathlib
p = pathlib.Path('index.html')
html = p.read_text(encoding='utf-8')
# walk visible <section class="page"> in order, rewrite footer-pg + div-foot-pg
# (full script in DEV_REQUIREMENTS.md § 9)
```

The renumber skips any `<section style="display:none">` so historical / parked pages don't bump the count.

## Status

v4 spec · v6 PDF · 32 pages · last updated 2026-06-19.
