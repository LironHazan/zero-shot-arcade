# Chord Coach — how the harnesses spent their twelve minutes

Same prompt, two agents, two harnesses. Both took about twelve minutes and both
delivered a working single-file toy. What differs is where the time went, and
that difference shows up in the artifacts.

## Measured: Claude Code / Opus 5

From the session transcript (`81b8d17e`), so these numbers are counted, not estimated.
Scratch workspace, `permissionMode: auto`, desktop entrypoint, v2.1.266.

| | |
|---|---|
| Prompt sent | 11:50:00 |
| File delivered | 12:02:24 — **12.4 min** |
| Assistant turns | 54 |
| Thinking blocks | 22 |
| Tool calls | 28 total, 24 in the build |
| Distinct tools | Bash ×12, browser_batch ×7, javascript_tool ×5, preview_start, resize_window, SendUserFile, ListAgents |
| Output tokens (build) | 148,478 · cache reads 5.94M |

The shape matters more than the total. **Zero tool calls for the first 7.6 minutes**,
then a single Bash call carrying a 36,681-character heredoc — the whole file, written
in one action. Then 4.8 minutes of verification: local http server, browser, viewport
probes, JS introspection, console checks. Delivered at 12.4 min.

That is why it *felt* faster than it was. Nothing visible happened for seven and a
half minutes, and then the artifact existed whole.

Notably it never used Write or Edit. Everything went through Bash, which is what the
harness asks for in auto mode.

## Reported: Workbuddy / hy4

From the agent's own summary, not independently verifiable:

- ~12 min, ~52 tool calls — **its own tally, with an explicit caveat that no
  authoritative counter is exposed to it**.
- 6 distinct tools: Bash, Write, Edit, Read, Grep, `present_files`.
- A theory test harness covering all 672 chords (12 roots × 2 scales × 7 degrees ×
  4 variations), Playwright smoke tests, 8 screenshot inspections, 5 bug fixes.
- Its audit log holds 34 records across 26 distinct tool-call ids, but it is a
  *security* log — sandboxed commands and approval-gated writes only, so it cannot
  stand in for a call count.

## Neither run was a single uninterrupted pass

Worth stating plainly, because it bears on the one rule. After writing the file, the
Claude Code run patched it three times (t+8.6m, t+10.1m, t+10.4m), seven replacements
in all:

1. **A real theory bug.** The first draft emitted `ø7` and a bare `+'7'` for seventh
   romans, so `Imaj7` would have read `I7`. Replaced with a quality-aware lookup.
   This is precisely the failure the prompt front-loads theory to prevent, and the
   model caught it in its own browser pass.
2. **Keyboard height, twice** — `clamp(90px,34vh,230px)` → `clamp(90px,42vh,260px)` →
   `min(100%,340px)`. That last value is the one in the landed file, and it is the
   cause of the dead space on tall phones.
3. Two `white-space:nowrap` fixes, and the mute button renamed from
   "Loop sound / Loop muted" to "Sound / Muted".

Workbuddy reports five Edit-tool bug fixes of its own.

So the rule here is not "the model's first tokens, untouched." It is **one prompt, one
delivered answer** — the agent's internal verify-and-fix loop happens inside that
answer. Retries are what's banned, and neither run retried.

## Where the testing looked is where the artifact is sound

The verification styles predict the weak spots almost exactly.

Workbuddy tested *theory* exhaustively and its theory is flawless — E♭ minor yields
`C♭`, the case that catches most implementations. But it shipped two faults only a
browser pass would surface: the register jumping between variations of one chord, and
`C♭3` where the pitch name is `C♭4`.

Claude Code verified *in the browser* and caught its own theory bug there, but left
the layout loose on tall screens.

Each artifact is weakest exactly where its own testing wasn't pointed.
