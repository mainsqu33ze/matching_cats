# Matching Cats 🐱

A single-file HTML5 match-3 puzzle game, built to be playable in iPhone Safari.
Made for my wife, with 29 procedurally generated levels and a final boss fight.

## Play it

Hosted on GitHub Pages: **<your-username>.github.io/matching_cats** once you push.

## How to play

- Tap two adjacent cats (or swipe) to swap them
- Match 3+ identical cats to score points
- Reach the goal score before you run out of moves to advance
- Some levels ask for deliveries: parcels slide down the highlighted lane and
  are handed over when they reach the bottom of the board, for 250 points each

## Powers

| Match        | Power              | Effect                                              |
| ------------ | ------------------ | --------------------------------------------------- |
| 4 cats       | 📦 Cardboard Box   | Tap a cat — cats of that type within 3 tiles jump in |
| 5 cats       | 🐟 Can of Tuna     | Tap any cat — clears all cats within 2 tiles        |
| 6+ cats      | 🎰 Slot Machine    | Clears the whole board and randomly refills it      |

Powers carry over between levels and are only reset when you start a new game.

## Save your progress

Your save code (level + total points) is shown at the start of every level, and
the game also auto-saves to your browser so you can keep going right where you
left off.

## Levels

- Levels 1–5: 5x5 grid, 4 cat types
- Levels 6–10: 6x6 grid, 5 cat types
- Levels 11–15: 6x6 grid, 5 cat types
- Levels 16–20: 7x7 grid, 6 cat types
- Levels 21–25: 7x7 grid, 6 cat types
- Levels 26–28: 8x8 grid, 7 cat types
- Level 29: 👑 FINAL BOSS

## Custom art

The game already supports custom art — drop PNGs into `assets/` and they
replace the emojis automatically:

- `assets/cats/` — one `cat0.png`–`cat7.png` per cat type (256x256 PNGs)
- `assets/fx/` — notes on where to hook in custom power effects

Any missing file simply falls back to the emoji. See the READMEs in those
folders for details.

## Music and sound

The ♪ button in the header (and on the welcome screen) turns all sound off and
on, and the choice is remembered between visits. Everything is generated in the
browser, so there are no audio files and nothing to download.

There is a looping background track, plus short sounds for matches, the board
refilling, an illegal swap, and game over. The match sound climbs in pitch
through a combo, so chains of matches sound like they are building.

Every power-up has its own sound too: a cardboard thud for the box, a can pop
for the tuna, a sparkle for the wand, and mechanical reel clicks with a jackpot
chime for the slot machine.

To change the tune, tempo or key, retune any of the effects, or swap in a real
track, see [`assets/MUSIC.md`](assets/MUSIC.md).

## Hosting on GitHub Pages

1. Push this folder to a GitHub repo (e.g. `matching_cats`)
2. Go to **Settings → Pages**
3. Under **Branch**, select `main` and `/ (root)`, then **Save**
4. Your game is live at `https://<your-username>.github.io/matching_cats/`

## Tech

A single `index.html` with zero dependencies — all HTML, CSS, and canvas JS
inline. No build step, no install, just open it.