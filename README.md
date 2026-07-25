# Sides

A random team generator that runs on any phone. Paste in the names, pick 2–6
teams, and draw. Don't like the split? Draw again. Names wrong? Fix them right
in the team sheet.

The whole app is one file — `index.html` — with no build step, no dependencies,
and no network calls. Fonts and logic are inlined, so it works offline once the
page has loaded.

## Using it

1. **Add people.** One name per line in the roster box. Commas and semicolons
   work too, so you can paste a list straight from a message.
2. **Pick how many teams** — 2 through 6. The counter tells you how big each
   team will be before you draw.
3. **Draw the teams.** Everyone is shuffled with `crypto.getRandomValues`, then
   dealt out so team sizes never differ by more than one.
4. **Draw again** until you're happy. A redraw never repeats the split you were
   just looking at.
5. **Edit anything.** Tap a name to correct a typo, or a team's title to rename
   it — "Bibs", "Skins", whatever. Renames survive a redraw.
6. **Copy** hands you a plain-text list ready to paste into a group chat.

A few conveniences worth knowing:

- Correcting a name in a team also corrects it in the roster — it's one person,
  not two records.
- Clearing a name entirely drops that person from the draw.
- Adding a name *after* a draw slots them onto the smallest team, so latecomers
  don't force a reshuffle.
- Your roster, team count, and current draw are saved on the device, so closing
  the tab doesn't lose them.

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

Color means exactly one thing in this app: team identity. Every control is
monochrome so the six team colors — the six colors a set of training bibs
actually comes in — never compete with anything. That's also why the app caps
at six teams, and why a live strip of those colors sits under the wordmark and
tracks the count you've chosen.

Type is Big Shoulders Display for the athletic condensed headings, IBM Plex Sans
for names, and IBM Plex Mono for counts and labels. All three are embedded as
base64 WOFF2 (the reason the file is ~150 KB) so nothing is fetched at runtime
and no fallback face can quietly substitute itself.

Both light and dark themes are defined as token sets; the app follows the
device's setting.

## Verified behavior

Checked in headless Chromium at an iPhone 13 viewport, in both themes:
everyone lands on exactly one team, sizes stay balanced within one, 400
consecutive draws spread pairings evenly, redraws never repeat, edits sync both
ways, state survives a reload, the layout never scrolls sideways, and every tap
target clears 40 px.
