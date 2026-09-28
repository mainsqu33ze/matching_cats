# Changing the background music

The music is **synthesised in the browser** with the Web Audio API — there is no
audio file. Nothing to download, nothing to cache, and it cannot 404. The whole
engine is one section in `index.html`, under the `// ---- BACKGROUND MUSIC ----`
comment near the top of the `<script>` block.

You do not need this file if you only want a different tune: the melody and bass
arrays are the only things you need to touch, and they explain themselves inline.

## The four things you can change

| Want to change        | Edit                        | Where                  |
| --------------------- | --------------------------- | ---------------------- |
| The tune              | `MELODY`, `BASS_ROOTS`      | `scheduleStep()`       |
| The tempo             | `MUSIC_STEP`                | music section header   |
| The key               | add an offset in the arrays | `scheduleStep()`       |
| Use a real audio file | see "Using a real audio file" below | `startMusic()` / `stopMusic()` |

## 1. Changing the tune

`MELODY` and `BASS_ROOTS` in `scheduleStep()` hold the actual notes, as **MIDI
note numbers**. Middle C (`C4`) is `60`, and each number is one semitone up:

```
60  C4      64  E4      67  G4      72  C5      76  E5
61  C#4     65  F4      68  G#4     73  C#5     77  F5
62  D4      66  F#4     69  A4      74  D5      79  G5
63  D#4                           75  D#5     81  A5
                                         83  B5      84  C6
```

A handy shortcut: `frequency = 440 * 2 ** ((n - 69) / 12)`, so `n - 69` is
semitones above `A4` (440 Hz). `noteFreq()` in the engine does this for you.

Rules to respect, or the loop will sound wrong:

- `0` means **rest**. It is a valid entry, not a missing one.
- Keep **8 numbers per bar** (eighth notes at the current tempo).
- Keep **4 bars**, so `MELODY` stays at 32 entries to match `MUSIC_STEPS`.
- `BASS_ROOTS` holds **one note per bar** — currently `[36, 33, 29, 31]`
  (C2, A1, F1, G1) under the C–Am–F–G progression. Change the melody's chords
  and these need to follow, or the two will clash.
- Update the trailing comments so the next reader knows what the chords are.

To hear it: open the game and press **Start Playing**. No reload needed beyond a
normal refresh.

## 2. Changing the tempo

`MUSIC_STEP` is the length of one eighth note in seconds, near the top of the
music section:

```js
const MUSIC_STEP = 0.3;          // one eighth note at 100 BPM
```

Faster = smaller number. A few reference points:

| `MUSIC_STEP` | Tempo |
| ------------ | ----- |
| `0.225`      | ~133 BPM |
| `0.26`       | ~115 BPM |
| `0.3`        | 100 BPM |
| `0.4`        | 75 BPM  |

## 3. Changing the key

Rather than renumbering every note by hand, shift the notes as they are read.
In `scheduleStep()`, change:

```js
const note = MELODY[step % MELODY.length];
if (note) playNote(note, time, MUSIC_STEP * 1.7, 'triangle', 0.20);
```

to:

```js
const key = 2;   // semitones to transpose; 2 = up a whole step
const raw = MELODY[step % MELODY.length];
const note = raw ? raw + key : 0;    // keep 0 as a rest, do not transpose it
if (note) playNote(note, time, MUSIC_STEP * 1.7, 'triangle', 0.20);
```

and do the same for the bass line:

```js
const root = BASS_ROOTS[Math.floor(step / 8) % 4] + key;
```

The `raw ? ... : 0` guard matters: a plain `raw + key` would turn every rest
into a real note. Transposing by `12` moves an octave.

Note the original reads into a `const`, so you must introduce a second variable
(`raw`) rather than reassigning `note` — assigning to it throws a
`TypeError`.

## 4. Using a real audio file

This is the one change that needs new code, because the current engine has no
`<audio>` element to point at. Keeping the synthesised loop as a fallback is
worth doing: a missing or slow-loading file would otherwise leave you with
silence where the generated version cannot fail.

1. **Add the file.** Create `assets/music/` and drop in something like
   `theme.mp3`. Loop it seamlessly — a short (10–30s) loop is plenty, and a file
   under a few MB keeps the game snappy on a phone.

2. **Add the element** next to the other overlays, just inside `<body>`:

   ```html
   <audio id="musicTrack" loop preload="auto" src="assets/music/theme.mp3"></audio>
   ```

3. **Point the engine at it.** In `startMusic()`, replace the scheduler body
   with something like:

   ```js
   function startMusic() {
       if (!music.enabled) return;
       const track = document.getElementById('musicTrack');
       if (!track) return;
       track.currentTime = 0;          // restart cleanly if stopped mid-song
       track.volume = 0;               // fade in from silence
       const target = 0.16;            // match the current music-box level
       track.play().then(() => {
           const steps = 20;
           for (let i = 1; i <= steps; i++) {
               track.volume = target * (i / steps);
           }
       }).catch(() => {});            // autoplay refused: stay silent
       updateMusicBtn();
   }
   ```

4. **Fade it back out in `stopMusic()`:**

   ```js
   function stopMusic() {
       const track = document.getElementById('musicTrack');
       if (!track) return;
       const target = track.volume;
       const steps = 10;
       for (let i = 1; i <= steps; i++) {
           track.volume = target * (1 - i / steps);
       }
       setTimeout(() => track.pause(), 350);
   }
   ```

5. **Keep the synthetic engine as a fallback** by leaving the existing functions
   in place and adding the file path in only when the track reports it loaded:

   ```js
   track.addEventListener('canplaythrough', () => { music.useFile = true; });
   ```

   then at the top of `startMusic()`, `if (music.useFile) { /* file version */ return; }`.

### What you do *not* need to touch

All of this lives in `setMusicEnabled()`, `updateMusicBtn()` and the event
wiring in `setupEventListeners()`, and is deliberately decoupled from *how* the
sound is produced. It carries over to a file-based track unchanged:

- the header and welcome-screen toggle buttons
- the on/off preference in `localStorage` under `meowMatchMusic`
- respecting the saved choice on load
- unlocking the audio context on the first real user tap
- pausing when the tab is hidden, resuming when it returns
- the greyed-out "muted" button state and its `aria-pressed` / `aria-label`

## Testing your changes

The music engine is covered by a test that stubs `AudioContext` and checks the
note scheduling without needing speakers. It verifies the graph builds, a full
4-bar loop emits the expected number of notes from the right pitch set, the step
counter wraps rather than drifting off the end of the arrays, and turning the
music off clears the timer and stops every oscillator.

```sh
node test_music.cjs        # from wherever the test files live
```

If you change `MELODY` or `BASS_ROOTS`, update the expected pitch set in that
test, or it will correctly fail and tell you the tune changed.

## One caveat

The tests confirm the notes are scheduled at sensible pitches. They cannot tell
you whether the result is *pleasant* — that needs ears. In particular, if you
change the key or the chords, listen on a phone speaker rather than headphones,
since that is how it will actually be played.
