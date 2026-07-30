# BlazeAPI announcement cards

Self-contained 1080×1080 HTML cards for social announcements. Each card is a
single HTML file with zero external dependencies — open it in a browser,
screenshot at 1080×1080, post it.

| File | Template | Use it for |
|---|---|---|
| `index.html` | Benchmark card — grouped bar chart on a tilted dark panel | Model launch / benchmark results |
| `pricing-card.html` | Plan card — model tiles, feature row, CTA pill | Pricing / plan announcements |
| `quant-card.html` | Performance card — KPI stat tiles + a QUANT-vs-benchmark line chart, recolored to a dark/green scheme | Trading / performance announcements; also the worked example of a full recolor |
| `usage-card.html` | Usage card — giant hero number in the headline + a 3-tile KPI row, in the base BlazeAPI cream/dark scheme | Lifetime/milestone stat announcements (e.g. total tokens processed) |
| `payg-card.html` | PAYG card — copy of pricing-card.html recolored to cyan `#22d3ee`, feature-square/CTA now read `DATA.accent` dynamically instead of hardcoded orange | Pay-as-you-go / new pricing-tier announcements |
| `icon.png` | Brand flame source image | Reference only — the cards embed a traced inline-SVG copy |

## Generate a card from your OWN data — step-by-step for agents

This is the recipe for Opus, Sonnet, or any coding agent asked to "make an
announcement card like this for <my thing>". Follow it in order. Do NOT
rebuild the layout from scratch — reuse a template file; only the `DATA`
block changes.

1. **Pick the template.** Comparing numbers across models/tools → copy
   `index.html` (bar chart). Announcing a plan/tier/product with a few
   highlights and a call to action → copy `pricing-card.html` (tiles + CTA).

2. **Copy, don't edit in place.** `cp index.html my-card.html` (or a name
   that describes the release). Leave the originals as clean templates.

3. **Collect the real data first.** Get the actual numbers/models/copy from
   the user — never invent scores or benchmark names. If a value is missing,
   ask or leave it out; do not fabricate a plausible-looking number. If the
   user hands you a screenshot or table, transcribe it exactly.

4. **Fill ONLY the `const DATA = {...}` block** (schemas below). Keep the
   fixed color scheme — hero entity first and orange `#ff8a2b`, competitors
   `#f2f2ee` → `#8a8a86` → `#55554f`. Set `headline.highlight` to the exact
   substring of `line1` you want in orange (usually the product/price).

5. **Do not touch CSS, markup, the `<script>` render logic, or the inline
   `<svg>` icon.** Everything scales from `DATA`. If the layout needs a real
   change (an extra row, a different tile count), that's a template change —
   see "Hard design rules" and keep every rule intact.

6. **Render and LOOK at it** (see "Exporting the PNG"). Screenshot at a true
   1080×1080 viewport, open the PNG, and read it. Check the "Verify before
   shipping" list. Iterate on `DATA` until it's right — never ship a card you
   haven't visually confirmed.

   For a full recolor to a different product's identity — different accent,
   different logo, dark-on-dark instead of cream-on-dark — see
   `quant-card.html`. It keeps the structure (tilted panel, `DATA`-driven
   render, flat fills, inline-SVG logo) but swaps the palette, the monospace
   type, the candlestick mark, and the chart type. That's the pattern:
   preserve structure and rules, change the scheme.

7. **Swapping the brand icon** (only if it's a different brand): the icon is
   an inline `<svg>` path traced from a source image, NOT an `<img>`. To
   replace it, trace the new logo to a single flat-fill path (threshold the
   image to its solid color, extract contours, simplify, drop into the
   `<path d="…">`) and keep it one flat color. Never link an external image.

Keep every "Hard design rule" below true in the output — they are what make
a new card look like it belongs to this set.

## How to make a new card (for AI agents and humans)

**Edit only the `const DATA = {...}` block** at the top of the `<script>` in
either file. Everything on the card — headline, spec line, models, scores,
tiles, features — renders from it. Do not touch the CSS or markup for a
routine content swap.

### `index.html` (benchmark card) DATA reference

```js
const DATA = {
  headline: {
    line1: "DeepSeek V4 Pro is now live", // big 80px line
    highlight: "DeepSeek V4 Pro",         // substring of line1 shown in the hero color ("" to disable)
    line2: "on BlazeAPI",                 // lighter 52px line
  },
  specs: ["1M Context Length", "132k output"], // joined with dot separators
  models: [
    // ORDER MATTERS: first model is the hero — orange bar, orange value labels.
    // Colors are the fixed scheme; keep them, just swap names.
    { name: "DeepSeek-V4-Pro-Max", color: "#ff8a2b" },
    { name: "Claude-Opus-4.6-Max", color: "#f2f2ee" },
    { name: "GPT-5.4-xHigh",       color: "#8a8a86" },
    { name: "Gemini-3.1-Pro-High", color: "#55554f" },
  ],
  benchmarks: [
    // scores are positional — one per model above, same order.
    // "\n" in a name makes a two-line label. sub is the small muted line, "" for none.
    { name: "SimpleQA\nVerified", sub: "", scores: [57.9, 46.2, 45.3, 75.6] },
    { name: "Codeforces", sub: "(Rating)", scores: [3206, 3166, 3052, 2988] },
    // ...
  ],
};
```

Behavior to know:
- Bars scale 0–100. If any score in a group exceeds 100 (e.g. a Codeforces
  rating), that group silently normalizes to its own max; the real values
  still print as labels.
- Values ≤ 100 display with one decimal (`40` → `40.0`); values > 100 display
  as integers.
- Six groups of four bars fit the card. A seventh group will run off the right
  edge (that can be a deliberate style — the panel clips it cleanly).

### `pricing-card.html` (plan card) DATA reference

```js
const DATA = {
  accent: "#ff8a2b",                       // hero orange, used for highlight + squares + CTA
  headline: { line1, highlight, line2 },   // same semantics as the benchmark card
  specs: ["Unlimited requests", "3 models", "Cancel anytime"],
  panelLabel: "Included models",           // small caps label, top-left of panel
  models: [                                // one tile each; swatch colors follow the scheme
    { name: "DeepSeek Flash", sub: "Fast everyday reasoning", swatch: "#ff8a2b" },
  ],
  features: ["No daily caps", "Swap models anytime", "Flat monthly price"],
  cta: "Start for $7/month",               // text inside the flat orange pill
};
```

## Hard design rules — never violate these

- 1080×1080 export frame; cream `#f7f4ea` top ~40%, near-black `#0a0a0c`
  tilted panel below (`perspective(1400px) rotateX(6deg)`, pivot on the
  bottom edge so the bottom bleeds full-width with no cream slivers).
- **Flat solid fills only.** No gradients, no box-shadows, no glow, no blur,
  no gloss.
- No purple. No emoji.
- Bars and CTA are fully rounded pills (`border-radius: 999px`).
- No external assets. The brand icon is an inline `<svg>` (a traced copy of
  `icon.png`, single flat color `#ff8a2b`) — keep it inline; never reference
  an image file or URL from the card.
- Hero model is always first and always orange `#ff8a2b`; competitors use
  `#f2f2ee` / `#8a8a86` / `#55554f` in that order.
- Text colors: headline `#141412` on cream, chart text `#f2f2ee` /
  `#e9e9e3`, muted `#8a8a86`. Serif (Georgia) for chart labels, sans
  (Helvetica) for headline and UI.

## Exporting the PNG

Screenshot at an exact 1080×1080 **viewport**. Beware: plain headless
Chromium's `--window-size` includes ~75px of window chrome, which crops the
card bottom. Use the headless shell (or Playwright with an explicit viewport):

```sh
chrome-headless-shell --disable-gpu --no-sandbox --hide-scrollbars \
  --screenshot=card.png --window-size=1080,1080 \
  --force-device-scale-factor=1 index.html
```

For 2× resolution use `--force-device-scale-factor=2` (output 2160×2160).

## Verify before shipping

Render the PNG and check: benchmark names visible near the panel bottom, no
value-label collisions, no cream showing at the panel's bottom sides, headline
fits on one line, and the DATA you swapped in actually appears.
