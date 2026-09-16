# Custom Cat Art

Drop your own cat artwork here and it will automatically replace the emoji
tiles in the game. Honest, it's automatic — the game is already wired to load
these files.

## File naming

| File                | Cat                                                                |
| ------------------- | ------------------------------------------------------------------ |
| `cat0.png`          | Plain cat face (`index.html` CAT_TYPES[0])                        |
| `cat1.png`          | Smiling cat with open mouth                                       |
| `cat2.png`          | Grinning cat with smiling eyes                                    |
| `cat3.png`          | Smiling cat with heart-eyes                                       |
| `cat4.png`          | Cat with wry smile                                                |
| `cat5.png`          | Kissing cat                                                       |
| `cat6.png`          | Weary cat                                                         |
| `cat7.png`          | Pouting cat                                                       |

## Requirements

- **PNG with transparent background**, **256x256** recommended
- The game scales each image to fit its tile, so square images work best
- Any file that is missing or fails to load simply falls back to the emoji,
  so you can replace cat 3 without touching the others

## Where the code uses this art

- Board tiles: `drawTileArt()` in `index.html`
- Power explosions (box / tuna / slot particles): the same `drawTileArt()`
  with `artIndex` set from the cat type