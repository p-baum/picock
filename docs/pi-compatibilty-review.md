# Pi compatibility review of Matt Pocock upstream skills

Reviewed upstream remote commit `272f99b22574f50e4266791c86b9302682970e23` in a detached worktree. Current changes on this fork/main were not reviewed.

Pi references used: Pi skill/package docs and README from the installed `@earendil-works/pi-coding-agent` docs. Key Pi facts for this review:

- Pi loads skills from package `pi.skills` entries or, if no Pi manifest exists, from conventional `skills/` directories.
- Pi registers skill commands as `/skill:<name>`, not bare `/<name>`.
- Pi supports `disable-model-invocation: true` and ignores unknown frontmatter fields.
- Pi has no built-in Claude Code `Agent`/`Task` sub-agent tool and no Claude Code hooks; those require Pi extensions or another explicit package.

## Summary

The upstream skills are mostly valid Agent Skills markdown, but the repo is not Pi-package compatible as-is. The biggest risks are:

1. A Pi install would discover a different skill set than Claude Code because `.claude-plugin/plugin.json` is ignored and there is no `pi` manifest.
2. Several advertised workflows require Claude Code sub-agents/background agents.
3. User-facing commands and cross-skill prose use Claude-style bare slash commands.
4. The guardrails skill configures Claude Code hooks, which do nothing in Pi.

## Findings

### High: No Pi package manifest, so Pi auto-discovers unintended skills

`package.json` has no `pi` key. Pi therefore falls back to the conventional `skills/` directory and recursively loads every `SKILL.md` under it. In this commit that means 38 skills, while `.claude-plugin/plugin.json` exposes only 20 promoted skills.

Extra skills Pi would load include drafts/deprecated/personal/Claude-specific items such as:

- `skills/in-progress/claude-handoff/SKILL.md`
- `skills/in-progress/wayfinder/SKILL.md`
- `skills/deprecated/design-an-interface/SKILL.md`
- `skills/deprecated/qa/SKILL.md`
- `skills/misc/git-guardrails-claude-code/SKILL.md`
- `skills/engineering/resolving-merge-conflicts/SKILL.md` (present, but not in the Claude plugin manifest)

`*.claude-plugin/plugin.json` is not a Pi manifest, so Pi will not use it to limit the installed skills. `scripts/link-skills.sh` has a similar issue for Pi's `~/.agents/skills`: it excludes only `deprecated/`, so it still links `in-progress/`, `misc/`, and `personal/` skills.

Recommended fix: add a `pi` manifest to `package.json` with an explicit skill allowlist/filter, and add the `pi-package` keyword if this should be discoverable as a Pi package. For example, either mirror the Claude plugin's promoted list or intentionally expose stable buckets while excluding `deprecated/` and `in-progress/`.

### High: Several workflows assume Claude Code sub-agents/background agents

Pi does not ship a built-in `Agent` or `Task` tool. The following upstream instructions will fail or degrade badly in default Pi:

- `skills/engineering/code-review/SKILL.md:58-74` tells the model to send two parallel `Agent` tool calls using the `general-purpose` subagent.
- `skills/engineering/improve-codebase-architecture/SKILL.md:22` tells the model to use `Agent` with `subagent_type=Explore`.
- `skills/engineering/codebase-design/DESIGN-IT-TWICE.md:19-32` tells the model to spawn 3+ parallel sub-agents using the Agent tool.
- `skills/engineering/research/SKILL.md:6` tells the model to spin up a background agent.
- If unintentionally exposed by Pi auto-discovery, `skills/deprecated/design-an-interface/SKILL.md:26`, `skills/deprecated/qa/SKILL.md:24`, and `skills/in-progress/claude-handoff/SKILL.md:8` are also Claude/sub-agent-specific.

Recommended fix: rewrite these skills with a Pi fallback: if a sub-agent extension/tool is unavailable, tell the user, run the passes sequentially with separate notes, and keep the outputs isolated. If true parallelism is desired, document an explicit Pi extension/package dependency instead of assuming Claude Code's `Agent` tool.

### High: Slash command syntax is wrong for Pi users

The repo consistently documents and routes skills as bare commands such as `/grill-me`, `/tdd`, `/setup-matt-pocock-skills`, and `/grilling`. Pi registers skills as `/skill:<name>`.

Examples:

- `README.md:31-38`, `README.md:58-59`, `README.md:144-183`
- `skills/engineering/ask-matt/SKILL.md:17-74`
- `skills/engineering/grill-with-docs/SKILL.md:7`
- `skills/productivity/grill-me/SKILL.md:7`
- `skills/engineering/triage/SKILL.md:76`
- All human-facing docs pages use `npx skills ...` and bare `/name` examples.

In Pi, a user typing `/grill-me` will not invoke the skill; they need `/skill:grill-me`. Cross-skill prose that tells the model to "run `/grilling`" is also likely to confuse execution.

Recommended fix: in Pi-facing docs and setup output, use `/skill:<name>` examples. In skill internals, prefer prose like "load/use the `grilling` skill" or explicitly use `/skill:grilling`.

### Medium: Claude Code hook guardrails do not work in Pi

`skills/misc/git-guardrails-claude-code/SKILL.md` sets up `.claude/settings.json` `PreToolUse` hooks and references `$CLAUDE_PROJECT_DIR` (`lines 24-81`). Pi has no Claude Code hook system, so this gives Pi users a false sense of protection.

Recommended fix: exclude this skill from Pi packages or replace it with a Pi-specific guardrails skill backed by a Pi extension using the `tool_call` event to block dangerous `bash` commands.

### Medium: Setup skill prefers `CLAUDE.md` over `AGENTS.md`

`skills/engineering/setup-matt-pocock-skills/SKILL.md:89-93` says to edit `CLAUDE.md` first and never create `AGENTS.md` when `CLAUDE.md` exists. Pi can read `CLAUDE.md`, but `AGENTS.md` is the Pi-oriented convention and avoids mixing Pi setup into Claude-only context.

Recommended fix: for Pi-oriented setup, prefer editing/creating `AGENTS.md` while preserving existing `CLAUDE.md` for Claude Code users.

### Medium: Install/update docs are not Pi-native

The README and docs pages tell users to install/update with `npx skills@latest add mattpocock/skills` and `npx skills update <skill>`. That may be fine for skills.sh/Claude-oriented flows, but it is not the Pi package workflow.

Recommended fix: add a Pi quickstart after adding a Pi manifest, e.g. `pi install git:github.com/mattpocock/skills@<ref>` (or the fork), then restart Pi or run `/reload`.

### Low: Claude-specific frontmatter fields are ignored by Pi

Some skills use `argument-hint` (`handoff`, `teach`, `claude-handoff`, `loop-me`). Pi ignores unknown frontmatter fields, so this is not a load failure. The promoted `handoff` and `teach` skills therefore include Pi-facing usage examples in their instructions and docs. The draft `claude-handoff` and `loop-me` skills are excluded from Pi distribution by the `!skills/in-progress/**` package manifest entry.

## Positive compatibility notes

- All `SKILL.md` files in this commit have valid `name` and `description` fields under Pi's documented skill validation rules.
- Existing `disable-model-invocation: true` usage is compatible with Pi.
- Relative links to helper docs/scripts are generally fine; Pi skill instructions support resolving relative paths from the skill directory.

## Suggested remediation order

1. Add `package.json` `pi.skills` filtering so Pi loads the intended skill set only.
2. Convert user-facing command examples and router prose to `/skill:<name>` for Pi-facing docs.
3. Add non-sub-agent fallbacks to `code-review`, `improve-codebase-architecture`, `codebase-design`'s design-it-twice reference, and `research`.
4. Exclude or port `git-guardrails-claude-code` to a Pi extension-backed skill.
5. Adjust setup docs to prefer `AGENTS.md` for Pi.
