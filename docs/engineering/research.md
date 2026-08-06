

## What it does

`research` answers a question by reading the sources that own the answer, then leaves a cited Markdown file in the repo. It works only from **[primary sources](https://www.aihero.dev/ai-coding-dictionary/primary-source)** — official docs, source code, specs, first-party APIs — and follows every claim back to the source that owns it, so it will not repeat a blog post's account of an API when the API's own docs are reachable.

It does not answer you in the conversation. The output is a file, written where the repo already keeps such notes, with a link on each claim. That is the point: a document you can react to, hand to another agent, or throw away, rather than an answer that vanishes when the [session](https://www.aihero.dev/ai-coding-dictionary/session) ends.

## When to reach for it


In Pi, invoke it with `/skill:research`. In Claude Code and harnesses where skills.sh installs bare commands, use `/research`. The agent can also reach for it automatically when a task turns into reading legwork.

Reach for it when the next step is *finding something out* — how an API behaves, what a spec actually says, whether a claim holds. For sharpening a plan by interview instead of by reading, use [grilling](https://aihero.dev/skills-grilling); for exploring what to build with throwaway code, use [prototype](https://aihero.dev/skills-prototype).

## Delegated legwork

The defining move is **primary-source legwork captured as an artifact**. In Pi, [`@tintinweb/pi-subagents`](https://github.com/tintinweb/pi-subagents) (`pi install npm:@tintinweb/pi-subagents`) supplies the background `Agent` tool, so you can keep working while one follows each claim to its source. Without that tool, the same research runs in the current session instead of pretending to be asynchronous. Either path drops a single cited Markdown file wherever the repo keeps such notes. Research is legwork you delegate, not thinking you outsource — you get back a document to react to, with its sources attached.


## Where it fits

A reach-for-it-anytime standalone that feeds the thinking skills rather than sitting in the build chain. Its file is something to take *into* the flow: [grilling](https://aihero.dev/skills-grilling) and [grill-with-docs](https://aihero.dev/skills-grill-with-docs) ask sharper questions when the facts are already on the table, and [to-spec](https://aihero.dev/skills-to-spec) can synthesise against it. [wayfinder](https://aihero.dev/skills-wayfinder) is the one skill that invokes it directly, resolving each research ticket on its map with a `/research` subagent. For the whole map, see [ask-matt](https://aihero.dev/skills-ask-matt).
