# Custom Power Effect Art

Powers don't have their own sprites yet — they currently reuse the cat art for
their explosion particles. If you'd like custom, per-power effect art, the
places to hook it in are all flagged in `index.html`:

| Effect                       | Where it lives in `index.html`                          |
| ---------------------------- | ------------------------------------------------------- |
| Box power (gold dashes)      | Power overlay color + preview in `render()`             |
| Tuna power (red dashes)      | Power overlay color + preview in `render()`             |
| Slot machine (purple dashes) | Power overlay color + preview in `render()`             |
| Explosion particles          | `createParticles()` / particle draw in `render()`       |

## Adding a per-power sprite

1. Drop e.g. `box.png`, `tuna.png`, `slot.png` into this folder.
2. In `applyBoxPower()` / `applyTunaPower()` / `applySlotPower()`, load the
   image and draw it centered on the affected area with `game.ctx.drawImage()`
   right before the particles spawn.

The overlay/preview colors are easy to change too:

- Box: gold `#d4af37` (overlay + preview in `render()`)
- Tuna: red `#dc3c3c`
- Slot: purple `#764ba2`