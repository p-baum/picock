---
name: research
description: Investigate a question against high-trust primary sources and capture the findings as a Markdown file in the repo. Use when the user wants a topic researched, docs or API facts gathered, or reading legwork delegated.
---

Delegate the research to a background sub-agent so the user can keep working while it reads. In Pi, use the `Agent` tool from [`@tintinweb/pi-subagents`](https://github.com/tintinweb/pi-subagents) with `subagent_type: "general-purpose"` and `run_in_background: true`. If the tool is missing, tell the user they can install it with `pi install npm:@tintinweb/pi-subagents` and restart Pi; for this run, perform the research in the current session and do not claim it is running in the background.

The research job:

1. Investigate the question against **primary sources** — official docs, source code, specs, first-party APIs — not a secondary write-up of them. Follow every claim back to the source that owns it.
2. Write the findings to a single Markdown file, citing each claim's source.
3. Save it where the repo already keeps such notes; match the existing convention, and if there is none, put it somewhere sensible and say where.
