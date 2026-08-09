# Remove Deck-Count Text From Cards + Add Flemish "How to Play" Page Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Stop printing "N in this deck" on each teaching card, and ship a new Flemish-language "Hoe te Spelen.html" page that teaches someone how to actually play the game with this card deck.

**Architecture:** `Chess Moves Cards - Standalone.html` is not plain hand-authored HTML — it is a **downloaded Claude Artifact bundle**. The real page markup/JS lives as a JSON string inside `<script type="__bundler/template">` on line 386 of the file; a loader script earlier in the file (`<script>` block starting around line 29) `JSON.parse()`s that string at page-load time and mounts it. Editing this file means editing *inside that JSON string* with exact-text replacements, preserving its escaping conventions (see Global Constraints). The new "Hoe te Spelen.html" page has no per-card repeat-generation need, so it's authored as an ordinary static HTML file — no bundler wrapper required.

**Tech Stack:** Plain HTML/CSS/inline JS. No build step, no server, no test framework — this is a static-file project (see `README.md`).

## Global Constraints

- Do not touch the `<script type="__bundler/manifest">` block (line 373-ish) or any `uuid`-shaped resource references (e.g. `0b03e493-4450-4d53-ae08-a5528f2420eb`, font `url("945aca62-...")`) anywhere in the file — these are resolved by the bundler's loader script at runtime and must stay byte-identical.
- Inside the `__bundler/template` JSON string, every HTML **closing** tag must be written as `</tag>` (backslash-u-zero-zero-two-F), never a literal `</tag>` — the outer document's HTML parser scans for a literal `</script` sequence before the JSON is ever parsed, and a literal closing tag inside the string would truncate the `<script type="__bundler/template">` block prematurely. Opening tags (`<div>`, `<a href="...">`, etc.) are written literally, same as the existing content. Follow the exact pattern already used throughout that block (e.g. `</div>`, `</script>`, `</a>`).
- Inside that same JSON string, literal double quotes in HTML attributes are written as `\"` (matching existing content, e.g. `style=\"...\"`).
- Do not change the `counts` object (`{ pawn: 26, knight: 6, bishop: 6, rook: 6, queen: 3, king: 3, repeat: 6 }`) — deck composition is unchanged, only the printed "N in this deck" text goes away.
- Do not add `@page` CSS or page-break rules, and don't restructure the existing `.page`/`doc-page` sections (per `README.md`'s fidelity note) — the only allowed visual change to the print area is removing the subtitle line itself.
- The new page's player-facing content must be written in Flemish (Vlaams/Dutch). All other project text (this plan, the spec, the README, code comments, commit messages) stays in English.
- Cross-reference links between the two files must use `%20` for spaces in the `href` (both filenames contain literal spaces on disk — keep the on-disk names as-is, but percent-encode spaces in the `href` attribute value for reliability across browsers/servers).

---

### Task 1: Remove "N in this deck" text from the card template

**Files:**
- Modify: `Chess Moves Cards - Standalone.html` (all edits are inside the single JSON string on line 386, inside `<script type="__bundler/template">`)

**Interfaces:**
- Consumes: nothing from other tasks.
- Produces: nothing consumed by Task 2 — the two files are independent; Task 2 only needs to know Task 1's final filename (`Chess Moves Cards - Standalone.html`, unchanged) for its own cross-link.

- [ ] **Step 1: Read the target region before editing**

Run (via the Read tool, not shell): read `Chess Moves Cards - Standalone.html` with `offset: 385, limit: 3`. This pulls in the `<script type="__bundler/template">` open tag, the full line-386 JSON string, and its closing `</script>` tag, so the exact current byte content is loaded before any edit.

Expected: the dump contains (among everything else) these seven exact substrings, each occurring exactly once in the file:

```
makeCard(name, subtitle, caption, glyph, data) {\n    const pc = this.cellCenter(data.pieceRC[0], data.pieceRC[1]);\n    const uid = this.uidCounter++;\n    const redId = `ah-red-${uid}`, blueId = `ah-blue-${uid}`;\n    return {\n      name, subtitle, caption, glyph, hasBoard: true, isRepeat: false,\n
```
```
makeCard('Pawn', `${counts.pawn} in this deck`, 'Moves 1 square forward (2 on first move); captures diagonally.', '♟', this.buildPawn())
```
```
makeCard('Knight', `${counts.knight} in this deck`, 'Moves in an L-shape: 2 squares one way, then 1 to the side.', '♞', this.buildKnight())
```
```
makeCard('Bishop', `${counts.bishop} in this deck`, 'Slides diagonally, any distance, in any direction.', '♝', this.buildBishop())
```
```
makeCard('Rook', `${counts.rook} in this deck`, 'Slides straight, any distance: up, down, left, or right.', '♜', this.buildRook())
```
```
makeCard('Queen', `${counts.queen} in this deck`, 'Slides any distance, any direction: straight or diagonal.', '♛', this.buildQueen())
```
```
makeCard('King', `${counts.king} in this deck`, 'Moves 1 square in any direction.', '♚', this.buildKing())
```
```
name: 'Play It Again', subtitle: `${counts.repeat} in this deck`, hasBoard: false, isRepeat: true,
```

If any of these don't match exactly (e.g. whitespace differs), stop and re-derive the exact substring from the freshly-read content rather than guessing — the JSON string is the single source of truth.

- [ ] **Step 2: Remove the subtitle `<div>` from the card template markup**

Using the Edit tool on `Chess Moves Cards - Standalone.html`:

old_string:
```
<div style=\"font-family:'Quicksand',sans-serif;font-weight:700;font-size:15px;letter-spacing:0.01em\">{{card.name}}</div>\n              <div style=\"font-size:9px;color:#93816c;margin-top:1px;font-weight:600\">{{card.subtitle}}</div>\n              <sc-if value=\"{{card.hasBoard}}\"
```

new_string:
```
<div style=\"font-family:'Quicksand',sans-serif;font-weight:700;font-size:15px;letter-spacing:0.01em\">{{card.name}}</div>\n              <sc-if value=\"{{card.hasBoard}}\"
```

This removes the whole subtitle line (and its leading newline/indentation) so there's no blank gap left under the card name.

- [ ] **Step 3: Drop the `subtitle` parameter from `makeCard()`**

old_string:
```
makeCard(name, subtitle, caption, glyph, data) {\n    const pc = this.cellCenter(data.pieceRC[0], data.pieceRC[1]);\n    const uid = this.uidCounter++;\n    const redId = `ah-red-${uid}`, blueId = `ah-blue-${uid}`;\n    return {\n      name, subtitle, caption, glyph, hasBoard: true, isRepeat: false,\n
```

new_string:
```
makeCard(name, caption, glyph, data) {\n    const pc = this.cellCenter(data.pieceRC[0], data.pieceRC[1]);\n    const uid = this.uidCounter++;\n    const redId = `ah-red-${uid}`, blueId = `ah-blue-${uid}`;\n    return {\n      name, caption, glyph, hasBoard: true, isRepeat: false,\n
```

- [ ] **Step 4: Update the six `makeCard(...)` call sites to drop the subtitle argument**

Six separate Edit calls on `Chess Moves Cards - Standalone.html`, each old_string → new_string:

1.
old: `makeCard('Pawn', \`${counts.pawn} in this deck\`, 'Moves 1 square forward (2 on first move); captures diagonally.', '♟', this.buildPawn())`
new: `makeCard('Pawn', 'Moves 1 square forward (2 on first move); captures diagonally.', '♟', this.buildPawn())`

2.
old: `makeCard('Knight', \`${counts.knight} in this deck\`, 'Moves in an L-shape: 2 squares one way, then 1 to the side.', '♞', this.buildKnight())`
new: `makeCard('Knight', 'Moves in an L-shape: 2 squares one way, then 1 to the side.', '♞', this.buildKnight())`

3.
old: `makeCard('Bishop', \`${counts.bishop} in this deck\`, 'Slides diagonally, any distance, in any direction.', '♝', this.buildBishop())`
new: `makeCard('Bishop', 'Slides diagonally, any distance, in any direction.', '♝', this.buildBishop())`

4.
old: `makeCard('Rook', \`${counts.rook} in this deck\`, 'Slides straight, any distance: up, down, left, or right.', '♜', this.buildRook())`
new: `makeCard('Rook', 'Slides straight, any distance: up, down, left, or right.', '♜', this.buildRook())`

5.
old: `makeCard('Queen', \`${counts.queen} in this deck\`, 'Slides any distance, any direction: straight or diagonal.', '♛', this.buildQueen())`
new: `makeCard('Queen', 'Slides any distance, any direction: straight or diagonal.', '♛', this.buildQueen())`

6.
old: `makeCard('King', \`${counts.king} in this deck\`, 'Moves 1 square in any direction.', '♚', this.buildKing())`
new: `makeCard('King', 'Moves 1 square in any direction.', '♚', this.buildKing())`

- [ ] **Step 5: Remove the subtitle field from the "Play It Again" card object**

old_string:
```
name: 'Play It Again', subtitle: `${counts.repeat} in this deck`, hasBoard: false, isRepeat: true,
```

new_string:
```
name: 'Play It Again', hasBoard: false, isRepeat: true,
```

- [ ] **Step 6: Add a small on-screen-only "How to play" link (English text), hidden when printing**

old_string:
```
</head>\n<body>\n<x-dc>\n\n<helmet>
```

new_string:
```
</head>\n<body>\n<div id=\"htp-nav\" style=\"font-family:'Nunito Sans',sans-serif;text-align:center;padding:10px 8px;background:#faf6ee;color:#93816c;font-size:12px;font-weight:600\">New to this deck? <a href=\"Hoe%20te%20Spelen.html\" style=\"color:#3d6b96;text-decoration:underline;font-weight:700\">How to play →</a></div>\n<style>@media print{#htp-nav{display:none}}</style>\n<x-dc>\n\n<helmet>
```

The `@media print` rule hides this banner during printing/export, so the printable card pages stay pixel-identical to before this change.

- [ ] **Step 7: Verify no deck-count text remains and the file is still well-formed**

Run:
```bash
grep -c "in this deck" "Chess Moves Cards - Standalone.html"
```
Expected: `0`

Run:
```bash
grep -c "card.subtitle" "Chess Moves Cards - Standalone.html"
```
Expected: `0`

Run:
```bash
python3 -c "
import re, json
text = open('Chess Moves Cards - Standalone.html', encoding='utf-8').read()
m = re.search(r'<script type=\"__bundler/template\">\n(.*)\n  </script>', text)
json.loads(m.group(1))
print('template JSON still valid')
"
```
Expected: prints `template JSON still valid` (confirms the edits didn't break JSON string escaping — if this raises a `json.decoder.JSONDecodeError`, an edit introduced a stray unescaped quote/backslash and must be fixed before continuing).

- [ ] **Step 8: Manual visual check**

Open `Chess Moves Cards - Standalone.html` directly in a browser (double-click it or `open "Chess Moves Cards - Standalone.html"` on macOS). Confirm:
- No card shows a "N in this deck" line, and there's no visible blank gap where it used to be.
- A small "New to this deck? How to play →" line appears above the card pages on screen.
- Card layout, arrows, and boards otherwise look unchanged from before.

- [ ] **Step 9: Commit**

```bash
git add "Chess Moves Cards - Standalone.html"
git commit -m "Remove deck-count subtitle from cards; add how-to-play nav link

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 2: Add the Flemish "Hoe te Spelen.html" page

**Files:**
- Create: `Hoe te Spelen.html`

**Interfaces:**
- Consumes: nothing (plain static file, no shared code with Task 1).
- Produces: nothing consumed elsewhere — this is the final deliverable file.

- [ ] **Step 1: Create `Hoe te Spelen.html` with the following exact content**

```html
<!DOCTYPE html>
<html lang="nl">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Hoe Speel Je Dit Kaartspel?</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Quicksand:wght@600;700&family=Nunito+Sans:ital,wght@0,400;0,600;0,700&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#faf6ee;
    --card-bg:#fffdf8;
    --ink:#2b2420;
    --muted:#93816c;
    --border:#a3907a;
    --red:#b3453a;
    --blue:#3d6b96;
  }
  *{box-sizing:border-box}
  body{margin:0;background:var(--bg);color:var(--ink);font-family:'Nunito Sans',sans-serif;line-height:1.5}
  .wrap{max-width:720px;margin:0 auto;padding:48px 24px 80px}
  h1{font-family:'Quicksand',sans-serif;font-weight:700;font-size:32px;margin:0 0 4px}
  .subtitle{color:var(--muted);font-weight:600;font-size:15px;margin:0 0 32px}
  h2{font-family:'Quicksand',sans-serif;font-weight:700;font-size:20px;margin:0 0 12px;display:flex;align-items:center;gap:8px}
  .dot{width:10px;height:10px;border-radius:50%;background:var(--red);display:inline-block;flex:none}
  .dot.blue{background:var(--blue)}
  section{background:var(--card-bg);border:1.5px dashed var(--border);border-radius:14px;padding:20px 24px;margin-bottom:20px}
  ul,ol{margin:0;padding-left:22px}
  li{margin-bottom:8px}
  li:last-child{margin-bottom:0}
  p{margin:0 0 12px}
  p:last-child{margin-bottom:0}
  .nav{font-size:14px;font-weight:600;margin:0 0 24px}
  .nav a{color:var(--blue);text-decoration:none}
  .nav a:hover{text-decoration:underline}
  .tip::before{content:"♟ ";color:var(--red)}
  @media print{
    body{background:#fff}
    section{break-inside:avoid}
  }
</style>
</head>
<body>
<div class="wrap">
  <p class="nav"><a href="Chess%20Moves%20Cards%20-%20Standalone.html">&larr; Terug naar de kaarten</a></p>
  <h1>Hoe Speel Je Dit Kaartspel?</h1>
  <p class="subtitle">Een No Stress Chess-stijl kaartspel om schaken te leren</p>

  <p>Dit kaartspel leert je stap voor stap hoe de schaakstukken bewegen &mdash; en hoe je er een volledig partijtje schaak mee speelt. Je hebt daarnaast een schaakbord en twee sets schaakstukken nodig.</p>

  <section>
    <h2>Wat heb je nodig</h2>
    <ul>
      <li>Een schaakbord</li>
      <li>2 sets schaakstukken (wit en zwart)</li>
      <li>Dit kaartspel, geschud, met de beeldzijde naar onder</li>
    </ul>
  </section>

  <section>
    <h2>Opstelling</h2>
    <p>Zet de schaakstukken op hun gewone startpositie, net zoals bij gewoon schaak. Weet je niet meer hoe een stuk beweegt? Elke kaart in dit spel toont het met pijltjes op een klein bordje &mdash; gebruik ze gerust als geheugensteun terwijl je speelt.</p>
  </section>

  <section>
    <h2><span class="dot"></span>Niveau Een &mdash; Basisspel</h2>
    <ol>
      <li>Schud het kaartspel en leg het met de rugzijde naar boven neer als trekstapel.</li>
      <li>Om beurt (wit begint): trek de bovenste kaart en leg ze open op je eigen afleg-stapel.</li>
      <li>Verplaats een stuk van het type dat op de kaart staat, volgens de pijltjes erop.</li>
      <li>Kom je op een veld van de tegenstander terecht? Dan sla je dat stuk en haal je het van het bord.</li>
      <li>Je mag nooit over of op je eigen stukken bewegen.</li>
      <li>Kan geen enkel stuk van dat type zetten? Dan sla je die beurt over.</li>
      <li>Trek je een &ldquo;Speel Opnieuw&rdquo;-kaart? Dan verplaats je een stuk van hetzelfde type als de kaart die het laatst gespeeld werd &mdash; van jezelf of van je tegenstander, jij kiest.</li>
      <li>Is de trekstapel op? Schud dan beide afleg-stapels samen tot een nieuwe trekstapel en speel verder.</li>
    </ol>
  </section>

  <section>
    <h2>Winnen</h2>
    <p>Je wint door de koning van je tegenstander te slaan &mdash; dat betekent: er met een van je eigen stukken op landen. Zodra dat gebeurt, is het spel meteen voorbij.</p>
  </section>

  <section>
    <h2><span class="dot blue"></span>Niveau Twee &amp; Drie &mdash; Voor gevorderden</h2>
    <p>Heb je Niveau Een al een paar keer gespeeld? Dan kan je overstappen naar Niveau Twee of Drie voor wat meer denkwerk.</p>
    <ul>
      <li><strong>Niveau Twee:</strong> elke speler krijgt vooraf een hand van 3 kaarten.</li>
      <li><strong>Niveau Drie:</strong> elke speler krijgt vooraf een hand van 5 kaarten.</li>
    </ul>
    <p>Leg de rest van het kaartspel met de rugzijde naar boven als trekstapel neer. Op beide niveaus speel je dan zo:</p>
    <ol>
      <li>Trek &eacute;&eacute;n kaart en voeg ze bij je hand.</li>
      <li>Speel &eacute;&eacute;n kaart uit je hand, open op je afleg-stapel, en verplaats het bijhorende stuk.</li>
      <li>Kan je met die kaart niet zetten? Dan sla je die beurt over.</li>
      <li>Heb je 3 of meer identieke kaarten in je hand, of laat geen enkele kaart een zet toe? Dan mag je je hele hand inruilen voor nieuwe kaarten &mdash; maar je zet dan deze beurt niet.</li>
    </ol>
  </section>

  <section>
    <h2>Tips voor beginners</h2>
    <ul>
      <li class="tip">Durf risico's te nemen: je weet nooit welke kaart je tegenstander trekt, dus een stuk even laten staan is vaak geen probleem.</li>
      <li class="tip">Speel Niveau Een een paar keer voor je naar Niveau Twee of Drie overstapt &mdash; zo leer je eerst goed hoe elk stuk beweegt.</li>
      <li class="tip">Raakt de trekstapel op? Geen paniek, het spel gaat gewoon verder met de afleg-stapels samengeschud.</li>
    </ul>
  </section>

  <p class="nav"><a href="Chess%20Moves%20Cards%20-%20Standalone.html">&larr; Terug naar de kaarten</a></p>
</div>
</body>
</html>
```

- [ ] **Step 2: Verify the file is valid, well-formed HTML**

Run:
```bash
python3 -c "
import xml.dom.minidom as m
import re
html = open('Hoe te Spelen.html', encoding='utf-8').read()
# quick well-formedness smoke test: tag balance for the custom sections we care about
assert html.count('<section>') == html.count('</section>')
assert html.count('<div') <= html.count('</div>') + html.count('/>')
print('tag balance OK')
"
```
Expected: prints `tag balance OK`

- [ ] **Step 3: Manual visual check**

Open `Hoe te Spelen.html` directly in a browser (double-click it, or `open "Hoe te Spelen.html"` on macOS — no server needed). Confirm:
- Page renders standalone, in Flemish, with Quicksand headings / Nunito Sans body text loading correctly.
- Visual style (colors, dashed section borders, warm background) reads as a companion to the card deck.
- The "&larr; Terug naar de kaarten" links at top and bottom open `Chess Moves Cards - Standalone.html` correctly when both files sit in the same folder.

- [ ] **Step 4: Commit**

```bash
git add "Hoe te Spelen.html"
git commit -m "Add Hoe te Spelen.html: Flemish how-to-play page for the card deck

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```
