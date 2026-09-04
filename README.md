# Sides

A random team generator that runs on any phone. Paste in the names, pick 2–6
teams, and draw. Don't like the split? Draw again. Give each team the colour of
the bibs they're actually wearing, fix any name, and save the whole thing as a
PDF team sheet.

You don't have to start from a list, either — **Build my own** gives you empty
teams straight away, and you type names into them directly.

The app is one file — `index.html` — with no build step, no dependencies, and no
network calls. Fonts, logic and the PDF writer are all inlined, so it works
offline once the page has loaded.

## Using it

1. **Add people.** One name per line in the roster box. Commas and semicolons
   work too, so you can paste a list straight from a message.
2. **Pick how many teams** — 2 through 6. The counter tells you how big each
   team will be before you draw.
3. **Draw the teams.** Everyone is shuffled with `crypto.getRandomValues`, then
   dealt out so team sizes never differ by more than one.

   Or skip steps 1–2 entirely and hit **Build my own**: you get empty teams, and
   every team has an *+ Add someone* row you type straight into. The roster box
   fills itself in as you go, so the two routes stay in sync — build by hand
   first, then hit Draw again to reshuffle the same people if you want.
4. **Draw again** until you're happy. A redraw never repeats the split you were
   just looking at.
5. **Set each team's bib colour.** Tap the jersey on a team's header to open the
   picker: red, orange, blue, pink, black, white, green. The strip under the
   title tracks your choices, and text on the band flips between light and dark
   so it stays readable — including on the white and black bibs.
6. **Appoint a captain.** Each player row has a **C** badge — tap it and that
   player becomes captain. One per team: appointing someone else moves the
   armband, and tapping the current captain stands them down. The badge fills
   with the team's bib colour.
7. **Edit anything.** Tap a name to correct a typo, or a team's title to rename
   it. Colours, names and captains all survive a redraw.
8. **Copy** gives you a plain-text list for a group chat, with the captain
   marked. **PDF** saves a printable team sheet.

A few conveniences worth knowing:

- Correcting a name in a team also corrects it in the roster — it's one person,
  not two records.
- Clearing a name entirely drops that person from the draw; if they were
  captain, the team simply has none until you appoint another.
- Renaming a captain keeps the appointment — it's the same person.
- A redraw scatters everyone, so a captaincy only carries over if that player
  happens to stay on the same team. No team can ever end up with two.
- Adding a name *after* a draw slots them onto the smallest team, so latecomers
  don't force a reshuffle. You can also add them to a specific team with that
  team's *+ Add someone* row.
- An add row you open but never type into simply disappears; it is never counted
  as a person.
- **Clear all** takes two taps: the first arms it, the second wipes the roster
  and the draw. It deliberately does not use a `confirm()` dialog — browsers
  block modal dialogs inside sandboxed frames and silently return "cancel",
  which made the button appear dead when the app was embedded.
- Your roster, team count, colours, captains and current draw are saved on the
  device.

## The PDF

The sheet is written directly as a PDF rather than routed through the browser's
print dialog, so it comes out the same on every phone and needs no print
preview. It's A4, uses the base-14 Helvetica faces every reader already has, and
prints each team's bib colour as a solid band. Captains are set in bold with a
**C** at the right of their row, in the team's colour (grey for the white bib,
which would otherwise be invisible on paper).

Short draws are laid out as one full-width column; longer ones flow into two
columns and onto further pages, with continued teams marked `(cont.)`. Names are
encoded as WinAnsi, which covers Latin alphabets — for scripts beyond that, use
the browser's own Share → Print, which has its own stylesheet here and renders
with the embedded fonts.

If a browser blocks the download outright (some embedded frames do), the button
falls back to the print dialog.

## Opening it on a phone

Any of these work:

- **GitHub Pages** — in the repository's *Settings → Pages*, publish from this
  branch's root. The app is then at `https://<user>.github.io/<repo>/`, and you
  can add it to your home screen from the browser's share menu.
- **Send yourself the file** — `index.html` is self-contained, so emailing or
  AirDropping it to a phone and opening it works, with no connection needed.
- **Locally** — open `index.html` in any browser, or serve the folder with
  `python3 -m http.server`.

## Design notes

Colour means exactly one thing in this app: team identity. Every control is
monochrome so the bib colours never compete with anything, and the palette is
the set of bibs you'd actually hand out rather than an arbitrary spectrum. Each
colour ships with the ink that stays legible on it, checked to at least 4.5:1 in
both themes; white and black bands get a hairline so they never dissolve into
the panel behind them.

Type is Big Shoulders Display for the athletic condensed headings, IBM Plex Sans
for names, and IBM Plex Mono for counts and labels. All three are embedded as
base64 WOFF2 (the reason the file is ~165 KB) so nothing is fetched at runtime
and no fallback face can quietly substitute itself.

Both light and dark themes are defined as token sets; the app follows the
device's setting.

## Verified behavior

Checked in headless Chromium at an iPhone 13 viewport, in both themes: everyone
lands on exactly one team, sizes stay balanced within one, redraws never repeat,
edits sync both ways, colours, names and captains survive redraws and reloads,
no team ever holds two captains, the layout never scrolls sideways, and every
tap target clears 36 px.

The shuffle was measured over 60,000 draws across three roster shapes. Both who
you're paired with and which team you land in come out uniform — every
chi-square statistic fell below its degrees of freedom (for example 7.6 on df 9
for pairings at 11 people across 4 teams).

Clear all is tested inside a frame sandboxed without `allow-modals` — the exact
condition that broke it — alongside a check that `confirm()` does return false
there. Generated PDFs are re-parsed with an independent library to confirm the
xref table resolves, that all names survive column and page breaks, and that
exactly the captained rows carry the bold face and the C mark.
