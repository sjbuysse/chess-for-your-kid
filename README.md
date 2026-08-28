# Chess Moves Teaching Cards

A small game aid for teaching a kid how chess pieces move, played with a
shuffled draw pile instead of taking turns by choice: whichever piece type
your card shows is the one you must move.

## Pages

- **`index.html`** (homepage) &mdash; a fullscreen card. Tap it to draw
  another, weighted like the physical 56-card deck (pawn 26, knight/bishop/rook
  6 each, queen/king 3 each, "Play It Again" 6) but with pawn weight halved to
  13 so pawns don't dominate every draw. Links to the rules page.
- **`rules.html`** &mdash; mobile-friendly how-to-play page (in Dutch), covering
  setup, Level One (basic play), and Levels Two & Three (hand-of-cards variant).
  Links back to the card page.

Both are self-contained static HTML/CSS/JS files &mdash; no build step, no
server required. Open either directly in a browser, or serve the folder with
e.g. `python3 -m http.server` for local testing.

## Deployment

Served via GitHub Pages directly from the `main` branch root
(`https://sjbuysse.github.io/chess-for-your-kid/`). Pushing to `main` is the
whole deploy step.

## History

An earlier version of this repo's `index.html` was a printable 8-up card deck
(2.5"×3.5" cards, cut-and-play). That page was retired in favor of the
on-screen random-draw page above; its source is still recoverable from git
history if a print deck is needed again.
