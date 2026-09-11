# Original prompt (as sent)

Act as an expert piano teacher who is also a senior frontend engineer. Your task is to write a complete, functional, self-contained HTML file — a browser chord coach that shows a beginner the chords of a scale on a piano keyboard, in several variations, and lets them practise along.

The learner you are building for is an adult or older child who can find middle C and knows nothing else. Everything on screen should be nameable and practisable on a real piano.

Build it with vanilla JavaScript and the browser's built-in Web APIs only. Do not use any external library, framework, or CDN — no Tone.js, no VexFlow, no soundfont loaders, no audio samples, no webfonts. The Web Audio API and the DOM are all you get.

### 1. Music theory — this is data, not interpretation, and it must be exactly right

- The learner picks a **root** (all twelve) and a **scale**: major or natural minor. Nothing else — no modes.
- Scale degrees in semitones from the root: **major** = 0, 2, 4, 5, 7, 9, 11. **Natural minor** = 0, 2, 3, 5, 7, 8, 10.
- The seven diatonic **triads**, by degree:
  - Major: I major, ii minor, iii minor, IV major, V major, vi minor, vii° diminished.
  - Natural minor: i minor, ii° diminished, III major, iv minor, v minor, VI major, VII major.
- The seven diatonic **sevenths**, by degree:
  - Major: Imaj7, iim7, iiim7, IVmaj7, V7, vim7, viim7♭5.
  - Natural minor: im7, iim7♭5, IIImaj7, ivm7, vm7, VImaj7, VII7.
- **Note spelling follows the key signature of the selected key, and never mixes sharps with flats.**
  - Major keys spelled with sharps: G, D, A, E, B, F♯. With flats: F, B♭, E♭, A♭, D♭. C major uses naturals.
  - Natural minor keys spelled with sharps: A (naturals), E, B, F♯, C♯, G♯. With flats: D, G, C, F, B♭, E♭.
  - So F major shows B♭ and never A♯. Getting this wrong teaches the learner something false, so treat it as a correctness requirement.
- Each chord is shown in exactly **four variations**, always in this order:
  1. **Root position** — root, 3rd, 5th ascending.
  2. **First inversion** — 3rd, 5th, root.
  3. **Second inversion** — 5th, root, 3rd.
  4. **Diatonic seventh** — the four-note seventh chord above, in root position.
- Display the chord symbol (e.g. `Dm`, `Gmaj7`), its roman numeral in the selected scale, and the interval formula of the shown variation (e.g. `3rd – 5th – root`).

### 2. Audio: it must sound like a piano, not a synth

Synthesise every note additively with `OscillatorNode`s. The single most important requirement:

- **A piano note has no sustain plateau.** Attack, then continuous decay to silence, always dying away. Do not hold a flat tone while a key is down. An envelope with a sustain stage is the reason naive synths sound like organs, and it is the difference between this toy sounding right and sounding wrong.
- Attack under 10ms, then exponential decay to silence.
- Six partials per note at roughly 1/n amplitude.
- Upper partials decay two to three times faster than the fundamental.
- Decay time scales with register: around 3.5s at C3 falling to around 1.2s at C5.
- Detune the upper partials by a few cents progressively for slight inharmonicity, so it isn't sterile.
- A few milliseconds of quiet attack transient for the hammer.
- Set master gain so that eight simultaneous notes do not clip.
- Chords are triggered as an **arpeggio**: notes entering ascending, roughly 70–90ms apart.

### 3. iOS audio — iPhone and iPad are hard targets

Both of these produce a completely silent page if missed, so implement both:

- Create exactly one `AudioContext`, **lazily, inside the first user gesture** (`pointerdown` or `keydown`), and call `resume()` on it. Never construct or start audio before a gesture.
- Safari routes Web Audio through the ringer channel, so **a device with the mute switch on plays nothing**. Prime the audio session inside that same first gesture by playing a short silent looping `<audio>` element (a data-URI WAV is fine) so playback is routed correctly.
- Additionally, feature-detect `navigator.audioSession` and set its `type` to `'playback'` where it exists. Treat this as a bonus, not the mechanism the fix relies on.
- If `AudioContext` is unavailable entirely, the visual half of the toy must still work.

### 4. The keyboard

- A **fixed two-octave window, C3 to C5**, always fully visible. Never scroll it horizontally, never change how many keys are shown at different widths — the same chord must occupy the same shape on every device.
- Scale the whole keyboard to fit the available width. Keys get narrower on a phone; they never disappear.
- Correct piano geometry: black keys narrower, overlapping the white keys they sit between, in the 2–3 grouping.
- Every key shows its **note name**, spelled per the rule in section 1, behind a toggle that is **on** by default.
- Keys are playable individually by tap, click, or keyboard. Support several simultaneous touches.

### 5. Fingering numbers — the reason this beats a chord chart

Render the finger number on each highlighted key. Right hand by default, with a left-hand toggle.

- Right hand — root position: 1-3-5. First inversion: 1-2-5. Second inversion: 1-3-5. Seventh: 1-2-3-5.
- Left hand — root position: 5-3-1. First inversion: 5-3-1. Second inversion: 5-2-1. Seventh: 5-3-2-1.

Numbers read bottom note to top note in the order given. Highlight the chord tones in the accent colour and distinguish the root of the chord from the other tones.

### 6. Layout — one screen, no modes

Everything is visible at once. There is no Explore/Practice toggle, no hidden panel, no navigation. Stack it vertically in exactly this order, on every screen size:

1. Root selector and major/minor toggle.
2. The row of seven diatonic chords, labelled with both chord symbol and roman numeral.
3. The four variations as a compact row.
4. The keyboard — the visual anchor of the page.
5. The practice transport.

It must fit without vertical scrolling both at **800×600** (it will be viewed inside an iframe that size as well as full screen) and at **375×667** (iPhone portrait).

### 7. Practice loop

- Exactly **four preset progressions**, and the list changes with the selected scale:
  - Major: I–V–vi–IV, I–IV–V–I, ii–V–I, I–vi–IV–V.
  - Natural minor: i–iv–v, i–VI–III–VII, i–iv–VII–III, ii°–v–i.
- One chord per bar in 4/4. Tempo adjustable from 40 to 160 BPM, defaulting to 80.
- A **one-bar count-in** with an audible click before the progression starts.
- The loop plays each chord aloud by default, and highlights the chord currently sounding plus the one coming next.
- A **mute toggle that keeps the loop visibly running** — highlight and timing continue in silence, so the learner can play the chords on their own piano. A mute that stops the highlight is useless.

### 8. Input

- Touch and pointer first: use Pointer Events, set `touch-action: manipulation` so taps don't wait on double-tap zoom, and clear the tap highlight. Do not disable pinch zoom entirely.
- Keys `1`–`7` trigger the seven diatonic chords. `Space` toggles play/pause.
- Simultaneous input must work — pressing a chord key while the loop runs should sound.

### 9. Visual style

Use exactly this palette and typeface:

- background `#0f1117`, panels `#171a23`, borders `#262a36`, text `#e8eaf0`, dimmed text `#9aa1b1`, accent `#ffd166`.
- `font: ui-monospace, SFMono-Regular, Menlo, Consolas, monospace` throughout.
- The keyboard must still read as a piano — white keys light, black keys dark — with the accent colour used for highlighted chord tones.

### 10. Architecture and delivery

- **One HTML file**, with CSS and JavaScript embedded. No build step, no bundler, no imports.
- **No `<script type="module">`.** Module scripts do not load over `file://`, and this file must work when opened directly from disk with no server, as well as over HTTPS.
- Do not `fetch` anything, local or remote.
- It must not throw on load. An early exception in a single-file page leaves a blank screen with the reason hidden in a console the learner will never open.
- All state in plain JavaScript. No localStorage requirement.

Deliver the complete, fully commented HTML file as a single code block. Prioritise, in this order: the theory being correct, sound actually coming out on an iPhone, and the fingering being usable at a real piano.
