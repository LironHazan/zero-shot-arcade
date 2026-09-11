# Chord Coach — review

Prompt: [`prompts/chord-coach-prompt.md`](../../prompts/chord-coach-prompt.md)
Runs: `runs/claude/opus-5/chord-coach.html`, `runs/workbuddy-ai/hy4/chord-coach.html`,
`runs/workbuddy-ai/gpt-6-astra/chord-coach.html`

The same prompt, sent once to each model. Nothing below was fixed afterwards — the
one rule allows saving the file and nothing else.

---

## Opus 5 — `runs/claude/opus-5/chord-coach.html`

Verified in-browser before landing:

- Opens clean, no console errors, one `AudioContext` created on the first real
  gesture (`running`), 6 partials + 1 noise transient per note.
- F major spells `B♭ IV` and `E° vii°`; every black key flat, no A♯. `Fmaj7`
  correct. A minor gives `Am i · B° ii° · C III · Dm iv · Em v · F VI · G VII`,
  ii seventh `Bm7♭5`.
- No scrolling at 800×600 or 375×667. Practice loop counts in, marks now/next,
  beat dots advance, readout follows.
- Weak spot: keyboard caps at 340px and centres, so a tall phone (375×812)
  leaves large dead bands above and below it.

### Your take

## hy4 — `runs/workbuddy-ai/hy4/chord-coach.html`

Verified in-browser before landing:

- Opens clean, no console errors, audio confirmed the same way (one context,
  6 oscillators + 1 noise per note).
- Theory holds everywhere I pushed it, including the hard case: E♭ minor spells
  `E♭m Fdim G♭ A♭m B♭m C♭ D♭` — it gets C♭ rather than cheating to B.
  Sevenths correct in both scales.
- No scrolling at 800×600 or 375×667. Loop, count-in and next-marker all work.
- Adds a full computer-keyboard note mapping the prompt never asked for.
- Weak spot 1: register jumps between variations of the same chord — F root
  position lands F4–A4–C5 against the top of the window, F seventh drops to F3.
- Weak spot 2: octave numbers are computed from MIDI alone, so in E♭ minor the
  B key reads `C♭3` where the pitch name is `C♭4`.
- Landed with the 33 `data-page-node-id` attributes the preview tool injected.

### Your take

## gpt-6-astra — `runs/workbuddy-ai/gpt-6-astra/chord-coach.html`

Verified in-browser before landing:

- Opens clean, no console errors. Audio confirmed the same way (one context,
  6 partials + noise transient + compressor, `running`).
- The most careful iOS handling of the three: it gates unlocking on
  `event.isTrusted`, actually appends the silent `<audio>` element to the DOM,
  deliberately leaves its volume at 1 so the playback session really opens, and
  reports state in the UI — "Audio ready · check device volume".
- Separate manual and loop gain buses, so mute silences only the loop and your
  own key presses stay audible. The other two runs mute everything.
- Theory correct throughout: F major `B♭ IV` / `Edim vii°`; A minor and its
  sevenths; E♭ minor spells `E♭m Fdim G♭ A♭m B♭m C♭ D♭`.
- Gets the case hy4 missed: it labels the B key `C♭4`, not `C♭3` — octave is
  derived from letter plus accidental, not from the MIDI number.
- Register stays put across variations — F root F3–A3–C4, seventh F3–A3–C4–E4,
  first inversion A3–C4–F4. No octave jumping.
- Extras nobody asked for: voice disposal with a 40-voice cap, musical position
  preserved across tempo changes, resume mid-bar without a second count-in,
  per-key aria labels, computer-key letters printed on the keys.
- Deviations from the prompt: roots are a `<select>` dropdown rather than twelve
  visible buttons, and the chord row keeps showing triad symbols in seventh mode
  (the seventh appears only above the keyboard).
- Weak spot: it breaks "never scrolls" on purpose. `@media(max-height:560px)`
  sets `min-height:580px`, so at 375×520 the page scrolls (580 > 520) instead of
  clipping. Fine at both named targets and at 1440×900, where `main` caps at 760px.

### Your take

---

See [`harnesses.md`](harnesses.md) for how the three agents spent their time.
