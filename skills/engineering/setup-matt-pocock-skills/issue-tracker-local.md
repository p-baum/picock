# Issue tracker: Local Markdown

Issues and specs (you may know a spec as a PRD) for this repo live as markdown files in `.scratch/`.

## Conventions

- One feature per directory: `.scratch/<feature-slug>/`
- The spec is `.scratch/<feature-slug>/spec.md`
- Implementation issues are one file per ticket at `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01` — never a single combined tickets file
- Triage state is recorded as a `Status:` line near the top of each issue file (see `triage-labels.md` for the role strings)
- Comments and conversation history append to the bottom of the file under a `## Comments` heading

## Completion gates

Before setting implementation work to closed or resolved, add a `## Completion` section containing:

- **Acceptance criteria:** Every criterion checked individually with pass/fail evidence.
- **Landing evidence:** The intended target branch and either a merged PR/MR reference, a commit SHA reachable from that branch, or an explicitly identified superseding implementation. A closed, unmerged PR/MR is not completion.
- **Reproducible validation:** The exact commands or procedures run and their observed results, not unsupported prose such as "tests pass."
- **Distribution status:** For packaged or promoted skills, whether distribution/release is complete, pending, unnecessary, or blocked, with relevant manifest checks and release reference. A source commit alone does not prove distribution.
- **Review:** A dated independent-review note with the reviewer's identity for owner-authored work. If no reviewer is available, document why and self-review the diff scope, each acceptance criterion, validation results, target branch, distribution impact, and unresolved risks.

Before every issue or proposed-change closure, append an explanatory entry under `## Comments`, including completed, duplicate, superseded, declined, abandoned, and unmerged outcomes. Identify the outcome and, when applicable, the merged change, target-branch commit, or superseding implementation. Wayfinding decision tickets may use their lighter `## Answer` resolution flow, but still require the explanatory answer; the full completion record applies to implementation work.

## When a skill says "publish to the issue tracker"

Create a new file under `.scratch/<feature-slug>/` (creating the directory if needed).

## When a skill says "fetch the relevant ticket"

Read the file at the referenced path. The user will normally pass the path or the issue number directly.

## Wayfinding operations

Used by the `wayfinder` skill. The **map** is a file with one **child** file per ticket.

- **Map**: `.scratch/<effort>/map.md` — the Notes / Decisions-so-far / Fog body.
- **Child ticket**: `.scratch/<effort>/issues/NN-<slug>.md`, numbered from `01`, with the question in the body. A `Type:` line records the ticket type (`research`/`prototype`/`grilling`/`task`); a `Status:` line records `claimed`/`resolved`.
- **Blocking**: a `Blocked by: NN, NN` line near the top. A ticket is unblocked when every file it lists is `resolved`.
- **Frontier**: scan `.scratch/<effort>/issues/` for files that are open, unblocked, and unclaimed; first by number wins.
- **Claim**: set `Status: claimed` and save before any work.
- **Resolve**: append the answer under an `## Answer` heading, set `Status: resolved`, then append a context pointer (gist + link) to the map's Decisions-so-far in `map.md`.
