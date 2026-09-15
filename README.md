# Jazz Flash Chords

This is a browser app to practice jazz chord arpeggios by ear on saxophone.
The app shows a chord symbol. You play its notes into the microphone, in
any order and any octave. The app listens through pitch detection. It
marks each note as a hit or a miss.

By default, each chord also adds a 9th, natural or altered, on top of the
plain root, 3rd, 5th, and 7th. Turn that off in Settings to practice the
plain four-note chords instead.

The app needs no server code and no build step. It runs as static files in
a browser.

## Requirements

- A browser with support for the Web Audio API and `getUserMedia` (current
  Chrome, Firefox, Safari, or Edge)
- A microphone
- A local server for static files, because ES modules do not load over a
  `file://` URL

## Run the app

1. Start a static server from the project root.
   - `npx serve .`
   - `python3 -m http.server`
2. Open the printed URL in your browser.
3. Click **Start**.
4. Allow microphone access when your browser asks.

Start turns into a red **Stop** button once the app is running. Click it
to end the session: this releases the microphone and returns you to the
start screen. Click Start again to begin a fresh session.

## How a round works

The app shows a chord symbol. It gives you a one-second preview before it
starts to listen. Then the timer starts. The app listens for your chord
tones.

Play each note of the chord in any order and any octave. Each correct note
lights up its tone indicator. A round passes when you play every chord
tone before the timer runs out. A round fails when the timer runs out
first.

A wrong note does not fail the round on its own, and it does not undo a
tone you already hit. But it still shows up if you hold one long enough
for the app to register it. The result screen then adds a separate note,
apart from pass or fail: you fished for it.

After a round ends, do one of the following to move to a new chord:

- Press space.
- Click **Next chord**.
- Turn on auto-advance in Settings.

## Settings

Open the **Settings** panel to change these options. A change applies to
the next round, not the round in progress.

- **Timer**: seconds allowed per round.
- **Transposition**: the instrument key for the on-screen chord symbol
  (Concert, Alto Sax, or Tenor Sax). The app always works in concert
  pitch. This setting changes only the symbol you see on screen.
- **Auto-advance**: after a short pause, the app starts the next round on
  its own. Turn it off to control the pace yourself.
- **Include extensions/alterations**: on by default. Adds a 9th to every
  chord, on top of the plain root, 3rd, 5th, and 7th. Turn it off to
  practice plain four-note chords only.
- **Elimination mode**: off by default. Turns on a fixed pool of every
  root/quality combination in your current settings. Passing a round
  removes that combination from the pool. Failing does not. The pool
  refills once you clear it. While it is on, the game screen shows how
  many combinations are left.
- **Chord qualities**: which seventh-chord types feed the random pool
  (maj7, dom7, m7, m7b5, 7sus4). When extensions are on: maj7, m7, and
  m7b5 always get a natural 9th; dom7 picks at random from 9, b9, or #9;
  7sus4 picks at random from 9 or b9.
- **Roots**: which root notes feed the random pool.

Each checkbox group needs at least one checked box. The app blocks a
change that would leave a group empty.

## Type checking

The source files are plain JavaScript with JSDoc type comments. Nothing
compiles them, and the browser loads them as-is. To check the types,
install `typescript` and run:

```
npm install
npm run typecheck
```

## Project structure

- `index.html`: the page shell.
- `style.css`: layout and color.
- `src/chords.js`: chord data and transposition.
- `src/pitch-detect.js`: the YIN pitch detection algorithm and
  frequency-to-note math.
- `src/audio-input.js`: microphone setup.
- `src/game-state.js`: the round state machine and hit detection.
- `src/ui.js`: screen updates and input handling.
- `src/main.js`: the entry point that wires the other modules together.

## Tuning pitch detection

Three constants in `src/game-state.js` control how forgiving the match is:

- **`RMS_THRESHOLD`**: the volume floor a frame must clear before the app
  runs pitch detection on it. Raise it to ignore quiet breath noise. Lower
  it if soft notes go undetected.
- **`MIN_CONSECUTIVE`**: the number of matching frames in a row needed to
  count a note as a hit. Raise it to cut false positives from reed noise.
  Lower it for a faster response.
- **YIN threshold** (in `src/pitch-detect.js`, passed to
  `yinPitchDetect`): lower it for a stricter pitch match.

## Out of scope for this version

These stretch goals are not built. See `spec.md` for the full list.

- Guide-tone mode (3rd and 7th only)
- Progression mode (ii–V–I, blues changes)
- Required note order or direction
- On-screen cents display
- Register-specific practice
- Saved stats across sessions
