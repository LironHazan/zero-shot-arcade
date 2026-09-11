# Chord Coach — how three harnesses spent their time

One prompt, three agents. All numbers below are counted from each harness's own
session transcript, not from what the agents said about themselves. Where a figure
is a self-report, it says so.

| | Claude Code / Opus 5 | WorkBuddy / hy4 | WorkBuddy / astra |
|---|---|---|---|
| Session | `81b8d17e` | `e6729715` | `46659002` |
| Expert summoned | none | none | **FrontendDeveloper** |
| Prompt → delivery | **12.4 min** | **26.1 min** | **61.3 min** |
| First tool call | +7.6 min | +12.5 min | +0.7 min |
| HTML first written | +7.6 min | +15.2 min | +22.9 min |
| Tool calls | 28 (24 in build) | 61 | 75 |
| Model(s) on the calls | claude-opus-5 | hy4-preview 30 · **minimax-m3 31** | gpt-6-astra 75 |
| Self-fixes before delivery | 7 replacements in 3 Bash patches | 10 Edits | 11 Edits |
| Separate test scripts written | 0 | 5 (Playwright) | 3 (`.cjs` harnesses) |
| Context used | — | 152,087 / 200,000 | 89,831 / 400,000 |
| Credits (`credit_json`) | — | 66.16 | **862.24** |
| Permission mode | auto | bypassPermissions | bypassPermissions |

## The three shapes

**Opus 5 front-loads everything.** Zero tool calls for 7.6 minutes, then one Bash
call carrying a 36,681-character heredoc — the entire file in a single action —
then 4.8 minutes of browser verification, then delivery. It never used Write or
Edit; everything went through Bash, which is what its harness asks for in auto mode.
This is why it *felt* fastest: nothing visible happened, and then the artifact existed.

**hy4 thinks first, then grinds.** 12.5 minutes before its first tool call, the file
written at +15.2 min, then ten Edits interleaved with five Playwright scripts and
screenshot reads, delivered at +26.1 min. Its own summary claimed "~12 min" and
"~52 tool calls" — the transcript says 26.1 min and 61 calls. Its list of six
distinct tools was exactly right.

**astra plans first, in public.** Its very first action, 42 seconds in, is
`Skill: recommend-experts` — it summons a FrontendDeveloper expert before doing
anything else, then loads `agent-browser` and `find-skills`, then files three
`TaskCreate` tickets (build / validate / deliver). The HTML doesn't get written
until +22.9 min. Eleven Edits and three purpose-written test harnesses follow, and
it delivers at +61.3 min — roughly five times Opus 5's wall clock and 13× hy4's
credit spend. **Twenty of its 75 calls are task bookkeeping** (`TaskCreate`,
`TaskGet`, `TaskUpdate`, `TaskList`, `TaskOutput`): a quarter of its tool budget
went on tracking its own plan.

Its two `AskUserQuestion` calls and both `WebFetch` calls happened *after* delivery,
during the follow-up about session metadata. Nothing was fetched from the network
while the artifact was being built.

## The "hy4" run is half minimax-m3

Worth knowing, because this repo labels runs by model. In that session the transcript
attributes **31 of the 61 tool calls to `minimax-m3`**, not to `hy4-preview` — a
switch at 15:07:11 that lasts to the end of the run. Everything in the final stretch
is minimax's: four Edits at 15:07, the note-name investigation, the zoom and loop
smoke tests, the last fix at 15:11:03, the memory note, and the delivery itself.
Reasoning rows split 21 / 17 the same way.

The log records the switch but not the reason for it. Either way, the artifact in
`runs/workbuddy-ai/hy4/` is not the work of one model, and the folder name overstates
the case.

## Nobody delivered in one pass

Opus 5 patched its file three times after writing it (seven replacements), including
a real theory bug: the first draft emitted `ø7` and a bare `+'7'` for seventh romans,
so `Imaj7` would have read `I7`. It also moved the keyboard cap twice, landing on
`min(100%,340px)` — the value that causes the dead space on tall phones. hy4 made ten
Edits, astra eleven.

So the rule here is **one prompt, one delivered answer** — the verify-and-fix loop
happens inside that answer. Retries are what's banned, and none of the three retried.

## Where the testing looked is where the artifact is sound

The verification styles predict the weak spots almost exactly.

- **hy4** tested theory exhaustively and its theory is nearly flawless — E♭ minor
  yields `C♭`, the case that catches most implementations. But it shipped two faults
  only a browser pass surfaces: the register jumping between variations of one chord,
  and `C♭3` where the pitch name is `C♭4`.
- **Opus 5** verified in the browser and caught its own theory bug there, but left
  the layout loose on tall screens.
- **astra** spent the most time and wrote the most tests, and it is the only one with
  neither class of fault — correct `C♭4`, stable register, separate manual and loop
  audio buses. It paid about five times the wall clock for that.

Each artifact is weakest exactly where its own testing wasn't pointed.
