# Custom Power Effect Art

Drop PNGs in this folder to customize the power animations. Just like the cat
art in `assets/cats/`, any file that is missing falls back to a placeholder
drawn in code, so you can replace them one at a time.

## Box power

**File:** `box.png` — 256x256 PNG with a transparent background.

When you use the box power, this sprite drops onto the tapped tile, all of the
affected cats leap into it one at a time (using the cat art), then the box
wiggles and fades away.

- Loaded by `loadFxArt()` in `index.html`
- Drawn by `drawBoxSprite()` in `index.html`
- Animation: `animateBoxCollect()` in `index.html`

Until you add `box.png`, a placeholder cardboard box is drawn automatically.

## Tuna power

**File:** `tuna.png` — 256x256 PNG with a transparent background.

When you use the tuna power, this sprite pops onto the tapped tile, then every
affected cat swarms it all at once (concurrent flights, no stagger), vanishing
as they arrive. The can wiggles and pops out afterward.

- Loaded by `loadFxArt()` in `index.html`
- Drawn by `drawTunaSprite()` in `index.html`
- Animation: `animateTunaSwarm()` in `index.html`

Until you add `tuna.png`, a placeholder can is drawn automatically.

## Slot power

The slot machine doesn't have a sprite yet — it clears the whole board and
reuses the cat art for its removal particles:

| Effect                       | Where it lives in `index.html`              |
| ---------------------------- | ------------------------------------------- |
| Slot machine (purple dashes) | Power overlay color in `render()`           |
| Explosion particles          | `createParticles()` / particle draw          |

To add a `slot.png`, mirror the box pattern: load it in `loadFxArt()`, add a
draw helper, and call it from `applySlotPower()`.

Overlay/preview colors (in `render()`):

- Box: gold `#d4af37`
- Tuna: red `#dc3c3c`
- Slot: purple `#764ba2`
