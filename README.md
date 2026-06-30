# FIFA World Cup 2026 — Knockout Bracket

A single-file website (`index.html`) showing the World Cup 2026 knockout stage:
a **Round of 32 fixtures grid** and the **Round of 16 → Final bracket**, with live
indicators, countdowns, and auto-updating match status.

Live site (once GitHub Pages is on): `https://abhigyankumarpathak.github.io/FIFA-2026-WORLD-CUP-BRACKET/`

---

## How to edit it

Everything you change lives in **one place**: the `DATA` block near the bottom of
`index.html` (look for the big `⚽ EDIT EVERYTHING HERE` comment, around line 274).
You never touch the HTML or CSS.

After any edit: **save the file**, then **refresh the browser**. To publish the
change online, commit & push (see [Publishing](#publishing-changes) below).

Each match is one line that looks like this:

```js
{ kickoff:"2026-06-29T13:00:00-04:00", a:{team:"Brazil", flag:"🇧🇷", score:2}, b:{team:"Japan", flag:"🇯🇵", score:1}, status:"ft" },
```

| Field | What it means |
|-------|---------------|
| `a` / `b` | The two teams. `a` is the top row, `b` is the bottom row. |
| `team` | Team name (text). |
| `flag` | Flag emoji shown before the name. |
| `score` | The team's score. Number (`2`) or text (`"1 (4)"` for penalties). Leave `""` if not played. |
| `kickoff` | Date & time of the match (see [Times](#changing-the-date--time)). |
| `status` | `"auto"`, `"live"`, `"ft"`, or `"upcoming"` (see [Status](#changing-live--full-time-status)). |
| `winner` | *(optional)* `"a"` or `"b"` — only needed for penalty shootouts. |
| `pens` | *(optional)* `true` to show the **FT (P)** tag for a penalty result. |

---

## Changing the score

Find the match and edit the `score` values for `a` and `b`:

```js
// Before
{ kickoff:"...", a:{team:"Brazil", flag:"🇧🇷", score:""}, b:{team:"Japan", flag:"🇯🇵", score:""}, status:"auto" },

// After — Brazil leading 2–1
{ kickoff:"...", a:{team:"Brazil", flag:"🇧🇷", score:2}, b:{team:"Japan", flag:"🇯🇵", score:1}, status:"auto" },
```

- When a match is **full-time** (`status:"ft"`), the **higher score is automatically
  highlighted as the winner** (green row, gold number). You don't set the winner yourself.
- A blank score `""` shows nothing in the score column (use for matches not yet played).

### Penalty shootouts
Put the shootout result in parentheses inside the score, and tell it who won:

```js
{ kickoff:"...", a:{team:"Germany", flag:"🇩🇪", score:"1 (3)"}, b:{team:"Paraguay", flag:"🇵🇾", score:"1 (4)"}, status:"ft", winner:"b", pens:true },
```

This shows `1 (3)` vs `1 (4)`, highlights Paraguay (`winner:"b"`), and adds an **FT (P)** tag.

---

## Changing live / full-time status

The `status` field controls the badge and behavior:

| `status` | What you see |
|----------|--------------|
| `"auto"` | **Recommended.** The site figures it out from the clock: counts down → glows gold **⏳ SOON** in the final hour before kickoff → flips to **🔴 LIVE** at kickoff → flips to **FT** after the match window. |
| `"live"` | Forces the pulsing red **LIVE** badge on (use only if you want to override the clock). |
| `"ft"`   | Forces **full-time**; winner highlighted from the score. |
| `"upcoming"` | Forces the greyed "scheduled" look (hides scores). |

**Most of the time you should leave matches on `"auto"`** — they go live and finish on
their own based on `kickoff`. Just keep the `score` updated while it's live.

When a match goes **live**, any blank score automatically shows **0** — so a kickoff
displays as **0–0** without you typing anything. Update the numbers as goals go in.

### "Starting soon" highlight
In the **hour before kickoff**, an `"auto"` match glows gold with a **⏳ SOON** tag.
Change how early that starts by editing this line near the top of the script:

```js
const SOON_WINDOW_MIN = 60;   // minutes before kickoff a match is highlighted as "starting soon"
```

### How long a game stays "LIVE"
A match counts as live for **180 minutes** after kickoff, then auto-flips to FT.
To change that, edit this line near the top of the script:

```js
const LIVE_WINDOW_MIN = 180;  // minutes a match counts as "live" after kickoff
```

---

## Changing the date & time

Times use this format and are **always displayed in New York time (ET)** for everyone,
no matter where the viewer is:

```
"2026-07-19T15:00:00-04:00"
 └─ date ──┘ └ time ┘ └ offset ┘
```

- `15:00:00` = 3:00 PM (24-hour clock: `13:00` = 1 PM, `21:00` = 9 PM).
- `-04:00` = U.S. Eastern in summer (EDT). **Leave this as `-04:00`** for all 2026
  matches — that's what makes them show correctly in New York time.
- If a schedule gives you a different time zone, convert to ET first:
  `UTC−7 → +3h`, `UTC−6 → +2h`, `UTC−5 → +1h`, `UTC−4 → same`.
  (e.g. `12:00 UTC−7` becomes `15:00` ET → write `T15:00:00-04:00`.)

Upcoming matches automatically show a **countdown** ("Kicks off in 4d 18h").

---

## Advancing teams in the bracket

The bracket (`leftR16`, `leftQF`, `rightR16`, … `final`) starts mostly as **🛡 TBD**.
When a team wins and moves on, replace the placeholder slot with the real team.

```js
// Before — empty slot waiting for a Round-of-32 winner
{ kickoff:"...", a:{team:"Paraguay", flag:"🇵🇾", score:""}, b:{ph:"RD32 W5"}, status:"auto" },

// After — Sweden won and advanced into the bottom slot
{ kickoff:"...", a:{team:"Paraguay", flag:"🇵🇾", score:""}, b:{team:"Sweden", flag:"🇸🇪", score:""}, status:"auto" },
```

- A slot written as `{ph:"RD32 W5"}` (or anything with no `team`) shows **🛡 TBD**.
  The `ph` text is just a hidden note reminding you which feeder belongs there.
- To fill it, swap it for `{team:"...", flag:"...", score:""}`.
- The bracket is mirrored: `left…` = top half of the draw, `right…` = bottom half,
  meeting at the **Final** in the middle.

### Crowning the champion
When the Final is decided, set the champion name:

```js
champion: "Argentina",   // shows "🥇 Champions: Argentina" under the trophy
```

---

## The live ticker

The red pills under the title at the top of the page are **automatic** — any match
currently `live` (or `auto` within its live window) appears there with its running
score. You don't edit this; it's built from the match data.

---

## Publishing changes

This site is hosted on GitHub. After editing `index.html`:

```bash
git add index.html
git commit -m "Update results"
git push
```

If GitHub Pages is enabled (repo **Settings → Pages → Deploy from branch → main**),
the live site updates automatically a minute or two after you push.

---

## Quick reference

| I want to… | Do this |
|------------|---------|
| Update a score | Change `score:` for `a` and/or `b` |
| Mark a game finished | Set `status:"ft"` (winner auto-highlights) |
| Show a game as live now | Leave `status:"auto"` (or force `"live"`) |
| Record penalties | `score:"1 (4)"`, `winner:"b"`, `pens:true` |
| Change a kickoff time | Edit `kickoff:"...T**HH:MM**:00-04:00"` |
| Advance a team | Replace `{ph:"…"}` with `{team:"…", flag:"…", score:""}` |
| Set the winner of the tournament | `champion: "Team Name"` |
| Change how long "LIVE" lasts | Edit `LIVE_WINDOW_MIN` |
| Change when "SOON" starts | Edit `SOON_WINDOW_MIN` |
