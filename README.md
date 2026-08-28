# Chess Moves Teaching Cards

A small game aid for teaching a kid how chess pieces move, played with cards
instead of taking turns by choice: whichever piece type your card shows is
one you're allowed to move.

## Pages

- **`index.html`** (homepage) &mdash; gate page: pick Niveau 1, 2, or 3.
- **`level-1.html`** &mdash; fullscreen single card. Tap it to draw another,
  weighted like the physical 56-card deck (pawn 26, knight/bishop/rook 6
  each, queen/king 3 each, "Play It Again" 6) but with pawn weight halved to
  13 so pawns don't dominate every draw. Links to `level-1-rules.html`.
- **`level-1-rules.html`** &mdash; mobile-friendly how-to-play page (Dutch)
  for Level One. Links back to `level-1.html`.
- **`level-2.html`** &mdash; 3 cards on top (rotated 180&deg;) + 3 on the
  bottom, phone laid flat between two players so each side reads their own
  row right-side up. Tapping a card redraws just that one, same weighting as
  Level 1. Links to `level-2-rules.html`.
- **`level-2-rules.html`** &mdash; how-to-play page (Dutch) for the
  hand-of-3 variant: play a card whose piece can move, then tap it to refill.
  Links back to `level-2.html`.
- **`level-3.html`** &mdash; same idea as Level 2, scaled up: 5 cards on top
  (rotated 180&deg;) + 5 on the bottom. Links to `level-3-rules.html`.
- **`level-3-rules.html`** &mdash; how-to-play page (Dutch) for the
  hand-of-5 variant. Links back to `level-3.html`.

Every page is a self-contained static HTML/CSS/JS file &mdash; no build
step, no server required, no shared modules (the card/board-drawing logic is
duplicated per file on purpose, matching how this site has always worked).
Open any file directly in a browser, or serve the folder with e.g.
`python3 -m http.server` for local testing.

## Deployment

Served via GitHub Pages directly from the `main` branch root
(`https://sjbuysse.github.io/chess-for-your-kid/`). Pushing to `main` is the
whole deploy step.

## History

An earlier version of this repo's `index.html` was a printable 8-up card
deck (2.5"&times;3.5" cards, cut-and-play), later replaced by the on-screen
Level 1 page above. That page's source is still recoverable from git history
if a print deck is needed again.
