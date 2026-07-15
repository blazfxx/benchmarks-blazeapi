# Instructions for AI agents

This repo holds 1080×1080 social announcement card templates for BlazeAPI.
Full documentation lives in `README.md` — read it before changing anything.

**To generate a card from someone's own data**, follow the step-by-step
recipe in `README.md` ("Generate a card from your OWN data"): pick the
template, copy it to a new file, collect the real numbers/copy (never invent
them), fill only `DATA`, render, and visually verify.

The short version:

1. To make a new card, edit **only** the `const DATA = {...}` block in
   `index.html` (benchmark card) or `pricing-card.html` (plan card). The
   layout renders itself from DATA; CSS and markup stay untouched for
   content swaps.
2. Never add gradients, shadows, glow, blur, purple, emoji, or external
   assets. Flat solid fills only. The brand icon must stay an inline SVG.
3. Hero model/plan accent is always orange `#ff8a2b` and always listed first;
   competitor colors are `#f2f2ee`, `#8a8a86`, `#55554f` in that order.
4. Export by screenshotting at an exact 1080×1080 viewport (see README —
   plain headless Chromium's window chrome will crop the card; use
   chrome-headless-shell or Playwright).
5. After any change, render the PNG and visually verify it before delivering.
