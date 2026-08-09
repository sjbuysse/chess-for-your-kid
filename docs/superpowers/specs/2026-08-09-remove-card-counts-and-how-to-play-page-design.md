# Design: Remove deck-count text from cards + add a "How to Play" page

**Date:** 2026-08-09
**Status:** Approved

## Context

The project is a printable "No Stress Chess"-style teaching card deck
(`Chess Moves Cards - Standalone.html`), a self-contained HTML file that
generates 56 cards (Pawn, Knight, Bishop, Rook, Queen, King, "Play It Again")
laid out 8-up on Letter-landscape pages for printing.

Each card currently shows a subtitle line under the piece name stating how
many of that card type exist in the deck (e.g. "26 in this deck"), sourced
from the `counts` object in the file's inline JS
(`{ pawn: 26, knight: 6, bishop: 6, rook: 6, queen: 3, king: 3, repeat: 6 }`).

There is currently no page explaining how to actually play the game with
this deck — only a design/handoff README aimed at whoever maintains the
HTML file, not at players.

## Goals

1. Remove the "N in this deck" text from every card face. The deck still
   has the same composition (`counts` stays as-is) — we're only removing
   the printed indicator, not changing quantities.
2. Add a new standalone "How to Play" page that teaches someone how to
   actually play using this card deck, a standard chessboard, and two
   sets of pieces. **This page's player-facing content is written in
   Flemish (Vlaams/Dutch)** — the user's requested language for anything
   players actually read. All other project documentation (this spec,
   the README) stays in English.

## Non-goals

- No change to card counts, layout, arrow logic, board rendering, or any
  other visual/print specs called out in the README as final.
- Not reproducing or copying Winning Moves' official No Stress Chess
  rulebook text — rules are written originally, informed by how this
  specific deck behaves.
- Not covering the "Advanced Game" / Standard Chess transition (check,
  checkmate, castling, en passant, pawn promotion) — out of scope per
  user decision; only Level One, Two, and Three of the card-driven game.
- Not translating card text on `Chess Moves Cards - Standalone.html`
  itself, or this spec/README — language change applies only to the new
  "How to Play" page.

## Part 1 — Card change

In `Chess Moves Cards - Standalone.html`:

- Remove the `subtitle` argument/text (`` `${counts.X} in this deck` ``)
  from all seven `makeCard(...)` calls and from the `Play It Again` card
  object literal in the `constructor`.
- Remove the `{{card.subtitle}}` `<div>` from the card template (the
  `doc-page`/`sc-for` markup), so there's no leftover empty line/gap
  under the card name.
- `counts` object and everything else in the file is untouched.

## Part 2 — "How to Play" page (in Flemish/Vlaams)

New file: `Hoe te Spelen.html`, self-contained (inline CSS, Google Fonts
`<link>`/`@font-face` consistent with the card file — Quicksand for
headings, Nunito Sans for body), same warm palette (`#faf6ee` background,
`#2b2420` text, `#a3907a`/`#93816c` muted accents, `#b3453a` red /
`#3d6b96` blue as small accent touches only, not full boards).

Unlike the card file, this is a normal scrollable single page (not a
print-cut grid) — readable on screen or printed as plain pages via the
browser's native print dialog. No custom `<doc-page>`/`sc-for` templating
engine needed; plain static HTML is enough since there's no repeated
per-card generation.

### Content outline (written in Flemish on the page itself)

1. **Titel / intro** — wat dit kaartspel is, en dat het samen met een
   echt schaakbord en twee sets schaakstukken gespeeld wordt.
2. **Wat heb je nodig** — een schaakbord, 2 sets schaakstukken, dit
   kaartspel.
3. **Opstelling** — standaard startpositie (stukken zoals bij gewoon
   schaak); verwijst naar de bewegingskaarten zelf als referentie voor
   hoe elk stuk beweegt, in plaats van dat hier te herhalen.
4. **Niveau Een (Basis)**
   - Schud het kaartspel, leg het met de rugzijde naar boven als
     trekstapel.
   - Spelers wisselen om beurten af: trek de bovenste kaart, verplaats
     een stuk van het getoonde type.
   - Beweging/slaan: beweeg langs de lijn op de kaart, sla een stuk door
     erop te landen, nooit door of op je eigen stuk bewegen.
   - Moet zetten als een geldige zet mogelijk is; anders beurt overslaan.
   - "Speel Opnieuw"-kaarten: verplaats een stuk van het laatst gespeelde
     type van eender welke speler.
   - Als de trekstapel op is: schud beide afgelegde stapels samen tot een
     nieuwe trekstapel.
   - Win door de koning van de tegenstander te slaan.
5. **Niveau Twee & Drie** — spelers krijgen een hand kaarten (3 voor
   Niveau Twee, 5 voor Niveau Drie) in plaats van één per beurt te
   trekken; elke beurt: trek één kaart bij je hand, speel dan één kaart
   uit je hand en zet het bijhorende stuk; je mag je volledige hand
   inruilen voor een nieuwe (zonder deze beurt te zetten) als ze 3+
   dezelfde kaarten bevat of geen enkele geldige zet toelaat.
6. **Winnen** — het spel eindigt zodra je de koning van de tegenstander
   slaat.
7. **Tips** — 2-3 korte tips voor beginners (bv. het is oké om een stuk
   te riskeren, want je tegenstander trekt misschien niet de juiste
   kaart; speel Niveau Een een paar keer voor je naar Twee of Drie
   overstapt).

### Cross-referencing

Add a small, light-touch mention on each file pointing to the other
(e.g. a line near the top of `Hoe te Spelen.html` noting it's meant to be
used with the card deck, and a small note/link on the card file pointing
to `Hoe te Spelen.html`), using a relative link since the README's
deployment guidance keeps both files in the same folder. Each file must
still make sense and work if opened on its own — the reference is a
courtesy, not a dependency. The cross-reference text on the card file
(English) stays in English; the reciprocal note on `Hoe te Spelen.html`
is in Flemish.

## Testing / verification

No build step or test suite (static HTML). Verification is manual:

- Open `Chess Moves Cards - Standalone.html` in a browser, confirm no
  card shows a deck-count line and there's no visible layout gap.
- Open `Hoe te Spelen.html` directly in a browser (no server) and
  confirm it renders correctly standalone, is readable, in Flemish, and
  matches the visual style of the card deck.
