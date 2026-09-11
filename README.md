# zero-shot-arcade

**One prompt. No retries. Whatever comes out, goes in.**

A for-fun collection of things frontier models make in a single shot — little HTML5
games and browser music toys. One prompt, first answer, no follow-ups, no fixes. If it's
janky, it stays janky. That's the fun part.

Not a benchmark. Nothing here is rigorous.

## The one rule

Strict zero-shot. The only thing I'm allowed to do is save the code to a file.

## How the prompts get made

The zero-shot rule covers the *run*, not the thinking that goes into the prompt. With no
retries, the prompt is the only lever there is — so it's worth sharpening before it's sent.

From the chord coach onward, the prompts here are built with [Matt Pocock's
`grilling` skill](https://github.com/mattpocock/skills). It interviews you relentlessly
about the idea, mapping it as a design tree: every decision branches into the decisions
hanging off it, asked in rounds, nothing silently assumed. You answer until the tree is
exhausted, and the prompt falls out of what's left.


The prompt as sent lives in `prompts/`. The grilling happens before it, and isn't part of the run.

## What's inside

```
prompts/                  the prompts, exactly as they were sent
runs/<thing>/<model>/     the artifact, exactly as it came out
index.html                the arcade page — click and play
```

Games get played (my 10-year-old is the toughest reviewer on the project). Music toys
get noodled with until someone tells me to stop.

## Play them

→ https://lironhazan.github.io/zero-shot-arcade

## License

MIT.
