---
name: implement-spec
description: "Implement a specification in code."
disable-model-invocation: true
---

You have been provided a spec. This spec should have tickets associated with it, describing how to implement the spec.

The goal is a single integration branch that implements the entire spec. Where the configured tracker provides a review-request surface, that branch has one draft pull request or merge request.

The tickets are not a list of steps. They are a **task graph** with blocking relationships between them. This means there is always a **frontier** of tickets which are ready to be grabbed.

Communication to and from subagents should be sparse. Communicate primarily through **context pointers**: to the spec, tickets, research notes, and previous commits. Don't duplicate information already available via pointers.

**Implementer subagents** should be run in the background where possible for **maximum concurrency**.

## Steps

1. Read `docs/agents/issue-tracker.md`, then fetch the spec and tickets through its configured workflow. If the file is missing, stop and tell the user to run the `setup-matt-pocock-skills` skill. Read enough to understand the task graph.

2. (optional) Use an **exploration subagent** to conduct any exploration required by the tickets - relevant codebase files or external documentation. Ensure the exploration subagent can save files - it should save its markdown notes in a directory outside the repo, accessible by all future subagents. This lets **implementer subagents** focus on implementation rather than exploration.

3. Create the integration branch. If the configured tracker provides pull requests or merge requests, create a draft through that workflow and link the spec and tickets with its closing syntax. If no review-request surface is configured, record the integration branch in the tracker instead of inventing one.

4. Use **implementer subagents** to implement each ticket. Each implementer subagent should work in its own worktree, on its own branch.

5. Once an **implementer subagent** completes, merge its work to the integration branch with a **merger subagent**.

6. If this changes the **frontier** of available tickets, kick off more **implementer subagents** to work on the new tickets. This allows for maximum concurrency.

7. Once all tickets are complete, use the `code-review` skill on the integration branch. Fix all issues raised by the code review in a single **implementer subagent**.

8. Follow the configured tracker's completion gates. Mark the pull request or merge request ready when one exists; otherwise record completion and validation evidence against the spec and tickets without closing them prematurely.

9. Clean up all **implementer subagent** worktrees.
