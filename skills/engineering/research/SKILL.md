---
name: research
description: Investigate a question against high-trust primary sources and capture the findings as a Markdown file in the repo. Use when the user wants a topic researched, docs or API facts gathered, or reading legwork delegated.
---

If the harness provides background sub-agents, delegate the research so the user can keep working while it reads. Otherwise, tell the user that background research is unavailable and perform the research in the current session. Do not claim the work is running in the background unless a tool actually provides that behavior.

The research job:

1. Investigate the question against **primary sources** — official docs, source code, specs, first-party APIs — not a secondary write-up of them. Follow every claim back to the source that owns it.
2. Write the findings to a single Markdown file, citing each claim's source.
3. Save it where the repo already keeps such notes; match the existing convention, and if there is none, put it somewhere sensible and say where.
