# BlazeAPI announcement cards

Self-contained 1080×1080 HTML cards for social announcements. Each card is a
single HTML file with zero external dependencies — open it in a browser,
screenshot at 1080×1080, post it.

| File | Template | Use it for |
|---|---|---|
| `index.html` | Benchmark card — grouped bar chart on a tilted dark panel | Model launch / benchmark results |
| `pricing-card.html` | Plan card — model tiles, feature row, CTA pill | Pricing / plan announcements |
| `icon.png` | Brand flame source image | Reference only — the cards embed a traced inline-SVG copy |

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
