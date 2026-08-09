# Handoff: Chess Moves Teaching Cards (Printable Deck)

## Overview
A 56-card printable teaching deck for learning how chess pieces move, played with a shuffled draw pile instead of taking turns by choice. Pawn, Knight, Bishop, Rook, Queen, King, and "Play It Again" cards, each showing a 5x5 mini board with movement arrows. Laid out 8-up on US Letter landscape pages, ready to print and cut.

## About the design file
`index.html` is a **self-contained, offline-capable HTML file** — open it directly in any browser, no build step, no server. It is a finished design artifact, not application source in a framework (React/Vue/etc). Treat it as the reference for exact visual output; recreate/adapt it in whatever stack you deploy with if you need a build pipeline, analytics, etc. For a static print deck, deploying this file as-is (see Deployment below) is usually the simplest correct answer.

## Fidelity
**High-fidelity.** Colors, typography, layout, arrow geometry, and card counts are final. Treat pixel/measurement values below as authoritative.

## How the deck is generated
All 56 cards are produced by one small piece of inline JavaScript embedded in the file (a class that builds arrow paths per piece type, then renders them). There's no external data file. To change the deck:
- **Card counts**: find the `counts` object (`{ pawn: 26, knight: 6, bishop: 6, rook: 6, queen: 3, king: 3, repeat: 6 }`) and edit the numbers. Cards auto re-chunk into pages of 8.
- **Captions/names**: each `makeCard('Pawn', subtitle, caption, glyph, ...)` call — edit the string arguments directly.
- **Arrow logic**: methods named `buildPawn`, `buildKnight`, `buildBishop`, `buildRook`, `buildQueen`, `buildKing` compute arrow paths on a 5x5 grid (cell = 40 units, piece at grid row/col, center square is [2,2]). Knight arrows are drawn as bent (L-shaped) SVG paths on purpose — don't straighten them.
- The `<doc-page>` custom element (a small web component, also inline) owns page sizing/print behavior — it sizes each `.page` section to a fixed Letter-landscape sheet at print/export. Don't add your own `@page` CSS or page-break rules; work within the existing `.page` sections.

This file uses a couple of non-standard custom tags (`<x-dc>`, `<sc-for>`, `<sc-if>`, `{{ }}` placeholders) that are resolved at load by a small inline runtime script bundled at the top of the file — this is expected and the page is fully self-contained; you don't need any external service for it to work. If you'd rather have a plain static HTML/CSS output with no custom tags to hand to a simpler pipeline, open the file in a browser, use "View Page Source" after it renders (or ask me to export a fully-flattened static version) and take the rendered `<doc-page>` markup as-is — it's static per print, no interactivity to preserve.

## Layout specs
- Card size: 2.5in × 3.5in, 0.16in border radius, 1.5px dashed border (#a3907a)
- Page: US Letter, landscape (11in × 8.5in), 4×2 grid of cards, 0.1in gap, 0.3in page padding
- Board: 5×5 grid, 40 SVG units/cell, light `#efe0c3` / dark `#b98657` squares, 2px `#6b4a2f` border
- Arrow colors: red `#b3453a` (normal moves), blue `#3d6b96` (pawn forward), dashed for capture-only/first-move-only/continues-past-edge
- Fonts: Quicksand (700, card titles) + Nunito Sans (400/600/700, body/captions), loaded from Google Fonts

## Deployment
This is a static file — no backend, no build. Any of these work:
1. **Static host** (fastest): upload the HTML file as-is to Netlify, Vercel, GitHub Pages, or Cloudflare Pages. Drag-and-drop deploy works since there's nothing to build.
2. **Inside an existing app repo**: serve it from a `/public` (or equivalent static assets) folder and link to it, or embed the `<doc-page>` section into an existing page shell.
3. **Printing**: the page already responds correctly to the browser's native print dialog (Cmd/Ctrl+P) — no extra print CSS needed once deployed.

## Files in this handoff
- `index.html` — the complete, self-contained design (open directly in a browser)
