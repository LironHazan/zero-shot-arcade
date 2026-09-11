# Chord Coach — how three harnesses spent their time

One prompt, three agents.

**What these numbers are.** Every figure below is counted from the mechanical records of
each session — tool-call rows, their timestamps, and session configuration. Nothing here
comes from an agent describing its own run: those descriptions turned out not to match the
records, so they are excluded rather than reported.

**What these numbers are not.** The session logs are not published, so nothing in this file
is independently checkable. Treat it as one person's reading of three runs, not as a
measurement anyone can reproduce. There is one run per agent, so none of it separates a
property of the harness from a property of the day.

| | Claude Code / Opus 5 | WorkBuddy / hy4 | WorkBuddy / astra | Counted from |
|---|---|---|---|---|
| Session | `81b8d17e` | `e6729715` | `46659002` | session id |
| Expert summoned | none | none | **FrontendDeveloper** | skill-call rows |
| Prompt → delivery | **12.4 min** | **26.1 min** | **61.3 min** | first and last message timestamps |
| First tool call | +7.6 min | +12.5 min | +0.7 min | tool-call timestamps |
| HTML first written | +7.6 min | +15.2 min | +22.9 min | timestamp of the first write to the `.html` |
| Tool calls | 28 (24 in build) | 61 | 75 | tool-call rows |
| Self-fixes before delivery | 7 replacements in 3 Bash patches | 10 Edits | 11 Edits | patch and edit rows |
| Separate test scripts written | 0 | 5 (Playwright) | 3 (`.cjs` harnesses) | file writes |
| Context used | — | 152,087 / 200,000 | 89,831 / 400,000 | harness context field |
| Permission mode | auto | bypassPermissions | bypassPermissions | session configuration |

## The three shapes

**Opus 5 front-loads everything.** Zero tool calls for 7.6 minutes, then one Bash
call carrying a 36,681-character heredoc — the entire file in a single action —
then 4.8 minutes of browser verification, then delivery. It never used Write or
Edit; everything went through Bash, which is what its harness asks for in auto mode.
This is why it *felt* fastest: nothing visible happened, and then the artifact existed.

**hy4 thinks first, then grinds.** 12.5 minutes before its first tool call, the file
written at +15.2 min, then ten Edits interleaved with five Playwright scripts and
screenshot reads, delivered at +26.1 min.

**astra plans first, in public.** Its very first action, 42 seconds in, is
`Skill: recommend-experts` — it summons a FrontendDeveloper expert before doing
anything else, then loads `agent-browser` and `find-skills`, then files three
`TaskCreate` tickets (build / validate / deliver). The HTML doesn't get written
until +22.9 min. Eleven Edits and three purpose-written test harnesses follow, and
it delivers at +61.3 min — roughly five times Opus 5's wall clock. **Twenty of its
75 calls are task bookkeeping** (`TaskCreate`, `TaskGet`, `TaskUpdate`, `TaskList`,
`TaskOutput`): a quarter of its tool budget went on tracking its own plan.

Its two `AskUserQuestion` calls and both `WebFetch` calls happened *after* delivery,
during the follow-up about session metadata. Nothing was fetched from the network
while the artifact was being built.

## Nobody delivered in one pass

Opus 5 patched its file three times after writing it (seven replacements), including
a real theory bug: the first draft emitted `ø7` and a bare `+'7'` for seventh romans,
so `Imaj7` would have read `I7`. It also moved the keyboard cap twice, landing on
`min(100%,340px)` — the value that causes the dead space on tall phones. hy4 made ten
Edits, astra eleven.

So the rule here is **one prompt, one delivered answer** — the verify-and-fix loop
happens inside that answer. Retries are what's banned, and none of the three retried.

## Where the testing looked is where the artifact is sound

The verification styles line up with the weak spots. Unlike the timings above, this
section is checkable: the artifacts are in `runs/`, and every fault named here can be
reproduced by opening them.

- **hy4** tested theory exhaustively and its theory is nearly flawless — E♭ minor
  yields `C♭`, the case that catches most implementations. But it shipped two faults
  only a browser pass surfaces: the register jumping between variations of one chord,
  and `C♭3` where the pitch name is `C♭4`.
- **Opus 5** verified in the browser and caught its own theory bug there, but left
  the layout loose on tall screens.
- **astra** spent the most time and wrote the most tests, and it is the only one with
  neither class of fault — correct `C♭4`, stable register, separate manual and loop
  audio buses. It paid about five times the wall clock for that.

Each artifact is weakest exactly where its own testing wasn't pointed. With one run per
agent this is a pattern worth noticing, not a result: it would take repeats to tell it
apart from chance.
