# Riley Board Pack · Developer Requirements

**Status:** v4 spec (2026-06-19) — supersedes v3 (2026-06-16).

**Output:** 32-page landscape A4 PDF, generated server-side from `index.html` via Cloudflare Browser Rendering, saved to R2, link returned to Riley as the final agent deliverable.

**Live mockup:** https://hs-board-pack.pages.dev · source `hs-board-pack-mockup/index.html`

**Latest snapshot PDF:** `safetyculture-board-pack-2026-06-19-v4.pdf`

---

## 1 · Output spec

| Field | Value |
|---|---|
| Page size | 338.67mm × 190.5mm (landscape A4) |
| Page margin | 0 (full bleed) |
| Fonts | Work Sans only · 300/400/500/600/700/800/900 (single-family system; DM Serif Display removed 24/06/2026) |
| Print colour | `-webkit-print-color-adjust:exact` so gradients + radial fills render |
| Page break | `page-break-after:always` between sections |
| Resolution target | 144 dpi minimum on logos + screenshots; SVG inline where possible |

**Render command** (local dev parity):
```
chrome --headless=new --no-pdf-header-footer --no-margins --print-to-pdf=out.pdf file:///path/to/index.html
```

---

## 2 · Design system

CSS variables in `:root` (lines 9-16 of `index.html`):

| Token | Value | Use |
|---|---|---|
| `--emerald` | `#10B981` | Primary brand accent · bright |
| `--forest` | `#0F3D2E` | Deep brand · titles |
| `--darkGreen` | `#0E7663` | Headings on tinted surfaces |
| `--deepGreen` | `#012D27` | Darkest brand |
| `--text` | `#0F2A20` | Body text |
| `--textSec` | `#5C7570` | Secondary body |
| `--textMuted` | `#94A39E` | Eyebrows + labels |
| `--surface` / `--surfaceAlt` | `#F7F8F7` / `#FAFCFB` | Card backgrounds |
| `--border` / `--borderSoft` | `#E3E8E6` / `#EFF1F0` | Strokes |
| `--amberBg` / `--amberFg` | `#FEF3C7` / `#92400E` | Worksheet input affordance |

**Cover / divider gradient** (the "creative green" pattern applied 2026-06-19):

```css
linear-gradient(118deg,
  var(--deepGreen) 0%, var(--forest) 22%, var(--darkGreen) 45%,
  var(--emerald) 72%, #4ADE80 92%, #86EFAC 100%);
```

Plus two radial overlays (upper-right bright halo, lower-left cooler emerald) for depth. Reuse the same gradient on `.hero` strips inside content pages.

**Typography rhythm** (must remain consistent across the deck):

| Class | Font | Size | Use |
|---|---|---|---|
| `.h1` | Work Sans 800 | 24pt | Page H1 |
| `.h1 .accent` | Work Sans 800 | inherit | Emerald accent run inside the H1 |
| `.lede` | Work Sans 400 | 9.5pt | One-paragraph standfirst beneath the H1 |
| `.pageheader-eyebrow` | Work Sans 800 | 7.5pt UPPERCASE | Breadcrumb (top-left) |
| `.pageheader-client` | Work Sans 700 | 7.5pt UPPERCASE | Client + section identifier (top-right) |
| `.stat-value` | Work Sans 800 | 32pt | Hero stats |

**Logos**

| Slot | Asset | Position |
|---|---|---|
| Cover + dividers (reversed, on dark gradient) | `hs-logo-light.png` (Primary Logo Reverse_RGB) | Cover top-left + divider footer-left |
| Content-page footer (icon mark on white) | `hs-mark.png` (Growth Icon 1 Colour_RGB) | Content footer-left, 7mm tall |
| Client logo (per engagement, full colour) | e.g. `safetyculture-logo.svg` | Cover top-right (8mm) + every content `.pageheader-client` (4.5mm) |
| Reserved for light-bg primary logo | `hs-logo.png` + `hs-wordmark.png` (Primary Logo Colour_RGB) | Not in current pages; available for future light-bg slots |

**Header / footer pattern**

- Content pages use `.pageheader` (top: eyebrow + client identifier) + `.frame > .content` + `.footer` (HockeyStick mark left, page number right, emerald hairline through the middle).
- Dividers use `.bleed` full-bleed gradient + `.div-foot` (HockeyStick reversed logo + label left, page number right).
- The breadcrumb (top-left `.pageheader-eyebrow`) carries the section name only — no leading number.

**Page numbering**

Numbers are written into `.footer-pg` (content pages) and `.div-foot-pg` (dividers). They are renumbered sequentially in document order via a Python pass (any visible `<section class="page">` increments; sections with `style="display:none"` are skipped). Run the renumber after any add / move / hide.

---

## 3 · Page list (32 pages, sequential)

Legend for **Type**: `cover` (full-bleed title), `divider` (full-bleed section break), `content` (white `.frame`), `static` (no per-engagement data variability), `dynamic` (live values from platform).

### Section 0 · Cover + Agenda

| # | Page | Type | Title / breadcrumb | Source(s) | Variability per client | PDF vs Platform format |
|---|---|---|---|---|---|---|
| 01 | Cover | cover · dynamic | Title `Partnership Strategy Playbook.` + italic `{ClientPhase}.` (e.g. `Align Phase.`) | `CLIENT.name`, `CLIENT.logoUrl`, `engagement.phase`, brand gradient | Client name, client logo, phase | PDF only; platform shows the same content in `Riley.deliverables.R0_cover` view |
| 02 | Agenda | content · static (structure) + dynamic (section names) | `Agenda.` + **5** numbered entries | Section list pulled from `BOARDPACK_SECTIONS` constant. Entries: 01 Vision · 02 The Role of Partnerships · 03 The Commercial Impact of Partnerships · 04 How to Win · 05 Crawl, Walk, Run (which includes Key Areas of Focus / 24-month timeline as the last page within the section, not a separate agenda entry) | Section ordering + names are static across all clients. Sub-bullets summarise the pages inside each section | Platform shows this as the deliverable nav rail in the in-app view; PDF compacts to one slide |

### Section 1 · Vision (pages 03-06)

| # | Page | Type | Title / breadcrumb | Source(s) | Variability per client | PDF vs Platform format |
|---|---|---|---|---|---|---|
| 03 | HockeyStick Framework · 5 pillars | content · static | `The HockeyStick Partnerships Framework · five pillars to compounding partner revenue.` | Static narrative + bar-chart visual (`R3_PILLARS_SEED`). Passive/Active dotted zone dividers sit between bar boundary 6/15 + 9/15 and end at the bar base. | None | Identical |
| 04 | HockeyStick · Our Vision + Client roster | content · static | `Vision: To enable partnerships…` + client roster | Vision sentence is static. Client roster table comes from `RILEY.client_roster` constant — **DEV NOTE: in production, replace text labels with brand-mark SVGs from the client logo CDN (monochrome grayscale on this page).** | None (client roster is always HockeyStick's logo wall, not the current client's customers) | PDF uses text labels for now; production replaces with SVG logos |
| 05 | `{ClientName}` Partnership Vision | content · dynamic | `{ClientName} Partnership Vision` + the verbatim vision statement | `engagement.a1_vision_statement_a` (Alex A1 deliverable). Eyebrow templated with `{client.name}`. | Vision statement, client name in eyebrow | Identical |
| 06 | What's possible through partnerships (Revenue Gap) | content · dynamic | `What's possible through partnerships?` + closeable gap headline | `CLIENT.revenue_aud × {partnerSourcedPct, partnerTargetPct, peerMedianPct}`. Default `partnerSourcedPct=12%`, `partnerTargetPct=25%`, `peerMedianPct=30%` (Crossbeam top-quartile). | Revenue value, partner-sourced % today, target %, calculated closeable gap | Identical |

### Section 2 · The Role of Partnerships (pages 07-18)

| # | Page | Type | Title / breadcrumb | Source(s) | Variability per client | PDF vs Platform format |
|---|---|---|---|---|---|---|
| 07 | Divider · Partner Strategy | divider · static | `Partner Strategy.` (or section-specific title) | Static section header | Section title rotates per deck; client logo footer label | Identical |
| 08 | Ecosystem Prioritisation Map | content · dynamic (visual) | `Ecosystem prioritisation · map.` Full-bleed canvas image (`alex-a2-canvas.png`). | Image captured server-side from the **live Alex A2 portal page** (headless Chrome screenshot, footer-strip hidden). No surrounding card / border / shading. | Different per engagement (snapshot of that engagement's Alex A2 canvas) | PDF embeds a PNG snapshot; platform has an interactive canvas. **No square borders or shading** around the image — fills the page max. |
| 09 | Partner Categories matrix (Sam D8) | content · static | `Partner Categories · Definition, Value Add and Company Outcomes.` 8 columns × 3 rows | Static `R3_PARTNER_CATEGORIES_SEED` matrix (Sam D8 canonical). 8 categories verbatim: Technology / Reseller / Channel / Marketplaces / Referral / Affiliate / Strategic Alliances / OEM. | None (canonical) | Identical |
| 10 | GTM Models Are Shifting + Jay McBain | content · static | `Partners are expanding their services before, during and after the transaction.` Canalys lifecycle diagram | Quote attributed to Jay McBain (Chief Analyst, Canalys). **Real photo** at `jay-mcbain.jpg` in a surface-tinted attribution card. White background page (was full-bleed). Canalys diagram image at `canalysis-lifecycle.png`. | None (industry analyst commentary) | Identical |
| 11 | Ecosystem Maturity Report (Sam D3) | content · dynamic | `{ClientName} is {ClassificationBand} on the partnership ecosystem maturity scale.` Hero + composite score + radar + dimension table | `MATURITY_DEFINITIONS` + `MATURITY_BANDS` + `scoreMaturity(intake)` + `BENCHMARK_BY_STAGE[client.stage]`. Per-dimension scores from Sam D3 intake. | All scores + classification + band, radar shape, dimension table | Identical layout to in-app D3 view. PDF condenses to one page. |
| 12 | Partnership Readiness Diagnostic (Sam D4) | content · dynamic | `{ClientName} is {RAG} across {N} of 7 readiness categories.` Donut + 7-card RAG strip | `READINESS_DIMENSIONS` + `scoreReadiness(intake)`. 7 dimensions: People / Process / Programs / Technology / Data / Budget / Exec buy-in. Per-dimension score + RAG + blocker count. | All scores, RAG, blockers | Identical |
| 13 | Customer revenue left on the table | content · dynamic | `Customer revenue left on the table.` + 3 chart cards | `ALEX_A3.cohort_metrics` — three KPIs: win rate, average deal size, weekly active users. Each shows Direct vs With Partner vs Benchmark + lift %. Gap to benchmark headline e.g. `+19%`. Footer: missed-revenue commercial hook (gradient strip with `$X left on the table`). | All metric values + benchmark gap + missed revenue $ | Identical |
| 14 | Where to play, how to win · 5-question strategy (Riley R1) | content · dynamic | `Where to play, how to win.` 5 horizontal cards (Vision / Play / Win / Capabilities / Systems) | `RILEY.r1_strategy_pyramid` answers. Each card has a left green-gradient label panel (`75mm` wide) + right white body. Equal-height rows with `gap:4mm` for consistent vertical rhythm. | All 5 strategy answers | Identical |
| 15 | Customer journey · Partner types × Executive lens (Alex A4) | content · dynamic | `Partners across the customer journey.` 5-stage chevron + grid | `ALEX_A4.customer_journey`. 5 buyer stages with chevron labels; partner types per stage + KPIs that move + executive owners. Activation is the focus stage (highlighted forest). | Per-stage partner types, KPIs, owners | Identical |
| 16-18 | Mapping Partners to the Customer Journey · ICP 1/2/3 | content · dynamic | Each page eyebrows `Mapping Partners to the Customer Journey` + H1 e.g. `Technology · ISVs.` | ICP cards generated from the top-3 priority categories in `ALEX_A2.priority_categories`. Each card: 5 customer needs × 5 categories × stars × workflow narrative. Pages 16/17/18 = Technology / Strategic / Channel. | Top 3 categories per client (drives which 3 pages render); per-card text | Identical |

### Section 3 · The Commercial Impact of Partnerships (pages 19-23)

| # | Page | Type | Title / breadcrumb | Source(s) | Variability per client | PDF vs Platform format |
|---|---|---|---|---|---|---|
| 19 | Divider · The Commercial Impact of Partnerships | divider · static | `The Commercial Impact of Partnerships.` | Section header. Two-line break in title preserves the gradient surface area. | Section title only | Identical |
| 20 | Competitor Analysis matrix (Alex A6) | content · dynamic | `Competitor Analysis.` 7-criteria × N-competitor matrix + Total + score key | `ALEX_A6.competitor_matrix`. Default 3 competitors (Asana / Smartsheet / Monday.com); 7 criteria with 1-5 scores. Score key: 1=No / 2=Basic / 3=Developing / 4=Advanced / 5=Best in class. | Competitor list per client, scores per cell | Identical |
| 21 | Partnership GTM Channels · Yr 1 + Yr 2 (Riley R2 v4.1) | content · dynamic | `Partnership GTM Channels · Top-Down model · Yr 1 + Yr 2.` | Top of page: two Expected Partnership Revenue tiles (Yr 1 emerald accent, Yr 2 forest accent). Four channel cards (PLG / Marketing / Sales / Partner-led primary) — each card has a 3-col inner grid (row label | Yr 1 | Yr 2) with 7 rows: Leads, Trial Conv %, Trials (derived), Activation %, Activations (derived), Revenue %, Revenue (derived = Expected Partnership Revenue × Revenue %). Single Revenue Mix card with two bars stacked (Yr 1 emerald · Yr 2 forest) + shared 4-col categories grid at the bottom. | Expected Partnership Revenue per year, per-channel per-year 4 inputs (Leads, Trial Conv %, Activation %, Revenue %), shared category sub-lists per channel. | Platform R2 is interactive workshop worksheet; PDF flattens both years as read-only side-by-side numeric grids per card. |
| 22 | Bottom-Up Partnership Model · Yr 1 + Yr 2 (Riley R4 pyramid) | content · dynamic | `Bottom-Up Partnership Model · Yr 1 + Yr 2 funnel build-up.` Two inverted funnel pyramids side by side (Yr 1 emerald · Yr 2 forest), 6 layers each (Revenue apex → Activations → Trials → Leads → Active partners → Partner pool base) | `r3FunnelTotals(catRegionTable, topDown, year)` per year via `useR3()`. Each pyramid sized identically (60% top → 100% base) with emerald connectors between layers. Year label above each pyramid. | All 6 layer values × 2 years (Pool, Active partners, Leads, Trials, Activations, Revenue + per-layer sub-text) | Platform R4 has Yr 1 / Yr 2 toggle; PDF shows both years simultaneously side-by-side since there's no interactivity. |
| 23 | Bottom-Up · Category view + Reconciliation · Yr 1 + Yr 2 (Riley R4 v6.2.4) | content · dynamic | `Category view + reconciliation · Yr 1 vs Yr 2.` | TOP HALF: Category view per year, side by side (Yr 1 emerald left · Yr 2 forest right). Each side has 8 category cards in a 2-col grid showing $ revenue + active partner count per category. BOTTOM HALF: Reconciliation table per year, side by side. Each table is a 5-column grid (Metric / Top-Down R2 / Bottom-Up R3 / Variance / Status) with rows for Leads / Trials / Activations / Revenue. Top-Down = R2 Partner-Led channel slice per year; Bottom-Up = R3 cat × region SUM per year. Status flips emerald (≥ TD) or red (below). | Per-category $ + active count × 2 years, all 4 reconciliation rows × 2 years | Platform R4 uses year toggle; PDF shows both years simultaneously for executive-pack comparison. |

### Section 4 · How to Win (pages 24-27)

| # | Page | Type | Title / breadcrumb | Source(s) | Variability per client | PDF vs Platform format |
|---|---|---|---|---|---|---|
| 24 | Divider · How to Win | divider · static | `How to Win.` | Static | None | Identical |
| 25 | What we want from partners (Alex A9) | content · dynamic | `What we want from partners · across the journey.` 5-stage × 3-row grid (Partner activities / Partner value add / Business outcomes) | `ALEX_A9.partner_expectations`. Each cell carries 3-5 pills with maturity markers `C` (Crawl) / `W` (Walk) / `R` (Run). **Compact spacing** (font 7.5pt, gap 1mm, pill padding 1mm × 2mm) so all 3 rows × 5 stages fit on one page. | Per-cell expectations + maturity assignments | Platform shows a wider editable matrix per cell; PDF compacts the pills to fit. |
| 26 | What we offer partners (Alex A11) | content · dynamic | `What we offer partners.` Mirror-shape grid | `ALEX_A11.partner_offers`. Same 5-stage × 3-row structure (HockeyStick activities / value add / business outcomes) | Per-cell offers | Identical compact spacing pattern. |
| 27 | Partnership inputs + outputs (Alex A10) | content · dynamic | `Partnership inputs + outputs.` | `ALEX_A10.partnership_io`. Two-column layout (inputs we provide → outputs we get back). | All input/output rows | Identical |

### Section 5 · Crawl, Walk, Run (pages 28-32)

This is the final agenda section. **Key Areas of Focus is the last page inside this section, not its own agenda entry.** The 24-month timeline (page 31) is the visualised expression of the key areas of focus within the Crawl, Walk, Run phasing.

| # | Page | Type | Title / breadcrumb | Source(s) | Variability per client | PDF vs Platform format |
|---|---|---|---|---|---|---|
| 28 | Divider · Crawl, Walk, Run | divider · static | `Crawl, Walk, Run.` | Static | None | Identical |
| 29 | Key Workstreams (Riley R5 starred) — **single row of 4 vertical boxes** | content · dynamic | `Four key workstreams to make this partnership program a success.` | `RILEY_R5.workstreams` filtered by `is_starred === true`. Each box: workstream ID (WS-XX) + star + Effort + ROI pills + serif title + body + Owner + Window. WS-08 gets the forest-gradient treatment as the strategic anchor. Layout is **`grid-template-columns:repeat(4,1fr)` — single row**, not a 2 × 2 grid. | The 4 starred workstreams per client | Platform shows full workstream list with filter; PDF crystallises to the top 4 starred. |
| 30 | Crawl · Walk · Run Strategy (R6) | content · dynamic | `Crawl · Walk · Run Strategy.` Three columns (Days 1-90 / 90-180 / 180+) with bullets per phase | `RILEY_R6.crawl_walk_run`. Each column gradient: Crawl emerald → mint, Walk emerald → darkGreen, Run forest. | All bullets per phase | Identical |
| 31 | Key Areas of Focus · 24-Month Timeline (R8) | content · dynamic | Breadcrumb `Crawl, Walk, Run Strategy`. H1 `Key areas of focus · 24-month timeline.` | `RILEY_R8.timeline`. 9 swimlanes (WS-01 → WS-08 + Global playbook) plotted across 8 quarters (Q1-Q4 FY26 + H1-H2 FY27 + FY28). Bars colour-coded by priority (red high / amber medium / emerald low / gradient = sustained). **Row padding 3.5mm + bar height 5mm + bar label 7.5pt** so the 9 swimlanes spread evenly down the page. | Per-WS bar placement + label | Identical |
| 32 | Closing / Thank you | cover · static | Gradient bleed with closing message + HockeyStick logo + ABN | Static text. Same gradient as the cover. | None | Identical |

---

## 4 · Static vs Dynamic at a glance

**Pure static pages** (same content every engagement):
- 03 Framework
- 04 HockeyStick Vision + Client roster
- 07 / 19 / 24 / 28 Dividers
- 09 Partner Categories matrix
- 10 GTM Models Are Shifting + Jay McBain
- 32 Closing

**Per-engagement dynamic** (read from platform):
- 01 Cover (client name + logo + phase)
- 02 Agenda (section names listed verbatim; quantities are static)
- 05 Partnership Vision (verbatim Alex A1)
- 06 What's possible through partnerships (Jo revenue intake + Crossbeam benchmarks)
- 08 Ecosystem Prioritisation Map (live Alex A2 canvas screenshot)
- 11 Ecosystem Maturity Report (Sam D3 intake)
- 12 Partnership Readiness Diagnostic (Sam D4 intake)
- 13 Customer revenue left on the table (Alex A3 cohort metrics)
- 14 Where to play, how to win (Riley R1)
- 15 Customer journey (Alex A4)
- 16-18 Mapping Partners to the Customer Journey · ICP 1/2/3 (Alex A2 top-3 categories)
- 20 Competitor Analysis (Alex A6)
- 21 GTM Channels · Yr 1 + Yr 2 (Riley R2 v4.1)
- 22 Bottom-Up Partnership Model · two pyramids side by side (Riley R4 · Yr 1 + Yr 2)
- 23 Category view + Reconciliation · Yr 1 + Yr 2 (Riley R4 v6.2.4)
- 23 Category view + Reconciliation (Riley R4 Category view + R3 reconciliation)
- 25 What we want from partners (Alex A9)
- 26 What we offer partners (Alex A11)
- 27 Partnership inputs + outputs (Alex A10)
- 29 Key Workstreams (Riley R5 starred)
- 30 Crawl · Walk · Run Strategy (Riley R6)
- 31 24-Month Timeline (Riley R8)

---

## 5 · PDF vs Platform format differences

Most pages are pixel-faithful to the in-app view. The exceptions, where the PDF deliberately diverges from the platform for fit:

| Page | In-app form | PDF form | Why |
|---|---|---|---|
| 08 Ecosystem Map | Interactive radial canvas with drag-able nodes | Server-side PNG snapshot rendered at canvas resolution | Static medium; readers need a frozen frame |
| 21 GTM Channels Top-Down | Editable Yr 1 + Yr 2 worksheet (R2 v4.1) with amber inputs + Expected Partnership Revenue tiles | Read-only 4-card grid with per-card Yr 1 + Yr 2 inner columns (7 rows each) + single Revenue Mix card with Yr 1 + Yr 2 bars stacked + shared categories | Both years simultaneously visible in the static PDF (no toggle available) |
| 22 Bottom-Up pyramid | Interactive pyramid with Yr 1 / Yr 2 toggle | Two pyramids side by side (Yr 1 emerald left · Yr 2 forest right), 6 layers each with emerald connectors | Both years visible at once; no toggle in static output |
| 23 Reconciliation | Year-toggled category cards + reconciliation table (R4 v6.2.4) | Two side-by-side category-view grids (8 cards each, 2-col layout) above two side-by-side reconciliation tables (4 rows each) | Yr 1 + Yr 2 comparison without interaction |
| 23 Category view + Reconciliation | Two separate Riley R4 tabs | Single combined page (category strip + reconciliation table) | One-stop business-case page |
| 25 What we want from partners | Wide matrix with full-text pills per cell | Compact pills (font 7.5pt, gap 1mm, pill padding 1mm × 2mm) | Fit 3 rows × 5 stages onto one A4 landscape |
| 26 What we offer partners | Same as above | Same compact pattern | Same |
| 29 Key Workstreams | Full workstream list with filter | Top 4 starred only, 1 × 4 horizontal row | Executive focus |

---

## 6 · Client variability snapshot

Per-engagement values pulled from the engagement record:

| Field | Drives | Default |
|---|---|---|
| `client.name` | Title eyebrows, "vision" titling, section labels | (none) |
| `client.logoUrl` | Top-right of every content page + cover | (none) |
| `engagement.phase` | Cover italic eyebrow (e.g. "Align Phase.") | "Align Phase" |
| `client.revenue_aud` | Revenue Gap headline + closeable gap calc | 220M |
| `partnerSourcedPct` (Jo intake) | Today % in Revenue Gap | 12% |
| `partnerTargetPct` (Jo intake or Crossbeam) | 18-month target % | 25% |
| `peerMedianPct` (Crossbeam) | Benchmark % | 30% |
| Sam D3 intake (7 dimensions × score) | Ecosystem Maturity page | (per engagement) |
| Sam D4 intake (7 dimensions × score) | Partnership Readiness page | (per engagement) |
| Alex A1 vision statement | Page 05 hero | (per engagement) |
| Alex A2 canvas screenshot | Page 08 image | (per engagement) |
| Alex A2 top-3 categories | Pages 16/17/18 ICP rotation | (per engagement) |
| Alex A3 cohort metrics | Page 13 charts + missed revenue | (per engagement) |
| Alex A4 customer journey × partner types | Page 15 grid | (per engagement) |
| Alex A6 competitor matrix | Page 20 matrix | (per engagement) |
| Alex A9/A10/A11 | Pages 25/26/27 | (per engagement) |
| Riley R1 strategy answers | Page 14 5-question | (per engagement) |
| Riley R2 channel mix + Category view | Page 21 | (per engagement) |
| Riley R4 driver totals | Page 22 pyramid + Page 23 category + reconciliation | (per engagement) |
| Riley R5 starred workstreams | Page 29 4-box | (per engagement) |
| Riley R6 Crawl-Walk-Run | Page 30 | (per engagement) |
| Riley R8 24-month timeline | Page 31 | (per engagement) |

Branding stays constant — HockeyStick logos + colour tokens + Work Sans (single-family) since 24/06/2026.

---

## 7 · Build + render pipeline

1. **Source HTML:** `hs-board-pack-mockup/index.html` (single file, inline CSS in `<style>`).
2. **Assets in same folder:** `hs-logo-light.png` · `hs-mark.png` · `hs-logo.png` · `hs-wordmark.png` · `safetyculture-logo.svg` (client) · `alex-a2-canvas.png` (Alex A2 snapshot) · `canalysis-lifecycle.png` (Canalys diagram) · `jay-mcbain.jpg` (analyst photo).
3. **Render:** Cloudflare Browser Rendering invokes Chromium headless against the deployed Pages URL with `print-to-pdf` + page-size override matching `@page { size: 338.67mm 190.5mm }`.
4. **Output:** PDF uploaded to R2 (`r2://hs-board-pack/{engagementId}.pdf`). Signed URL returned to Riley.
5. **Deploy:** `npx wrangler pages deploy . --project-name=hs-board-pack`. Live at `hs-board-pack.pages.dev`.

**Local dev parity:**
```bash
chrome --headless=new --no-pdf-header-footer --no-margins \
       --print-to-pdf=out.pdf file:///path/to/index.html
```

---

## 8 · Acceptance criteria (v4)

- [x] 32 pages total; sequential page numbers `NN of 32` in document order
- [x] All breadcrumbs (`.pageheader-eyebrow`) carry section name only — no leading number
- [x] Cover: 'Strictly Private & Confidential' sits in a footer strip
- [x] Framework chart dotted dividers sit between bars (boundary 6/15 + 9/15) and end at the bar base
- [x] Ecosystem Prioritisation Map page has no surrounding card / border / shading
- [x] Jay McBain page has white background + photo (not monogram)
- [x] Benchmark gap reads `+19%` (not `+19pp`)
- [x] Mapping Partners to the Customer Journey eyebrow appears on each ICP page (16/17/18)
- [x] Page 13 title is `Customer revenue left on the table`
- [x] Page 19 divider title is `The Commercial Impact of Partnerships.`
- [x] Page 21 title is `Partnership GTM Channels · Top Down Model.`, no subtitle, all 8 categories in a single horizontal row
- [x] Page 22 Bottom-Up pyramid Partner Pool box uses the same emerald stroke as the other layers
- [x] Page 23 (Category view + Reconciliation) added as a follow-up to the Bottom-Up Model
- [x] Page 25 (What we want from partners) — all 3 outcome rows render fully
- [x] Page 29 (Key Workstreams) — single horizontal row of 4 vertical boxes
- [x] Page 31 (24-month timeline) — 9 swimlanes spread evenly with consistent vertical rhythm
- [x] Cover + dividers + hero strips use the new `118deg` gradient running deep forest → emerald → bright lime
- [x] All logos pulled from high-res Brand assets (`Primary Logo Reverse_RGB`, `Growth Icon 1 Colour_RGB`, `Primary Logo Colour_RGB`)

---

## 9 · Renumbering tool

Any time a page is added, hidden or moved, run the renumber pass before re-rendering:

```python
# tools/renumber_pages.py — walks <section class="page"> in document order,
# skips style="display:none", rewrites footer-pg + div-foot-pg with NN of TOTAL.
```

This guarantees no duplicate page numbers, no stale `of NN` references, and hidden sections (kept for diff history) are skipped without affecting numbering.

---

## 10 · Out-of-scope (next pass)

- Real client logo SVGs on page 04 (currently text labels with a DEV NOTE)
- Server-side render harness inside the Riley agent flow (currently using a local Chrome headless command)
- Per-section divider colours / motifs to differentiate the six sections
- Per-engagement timeline scale (currently fixed at 24 months / 8 quarters)
