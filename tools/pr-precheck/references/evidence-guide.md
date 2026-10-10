# Evidence guide: where evidence lives in a PR package

<!--
THIS IS THE PART YOU WRITE (third week running: the map stays in your
hands). Your tool uses this guide as its map: for every kind of
evidence a rubric check names, this file says WHERE to find it in a PR
package and WHAT GOOD LOOKS LIKE when you do.

The four families below are the harness's failure categories under
the names the eval README uses: plan fidelity = silent-drift, test
evidence = not-tested, diff quality = unreviewable, standards and
comms = standards-wall. A package that fails none of them is a
clear-accept. Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the plan-context block's scope pair and test
  plan, the candidate PR's diff, commits, description, or
  test-evidence section, the repo-facts block's template asks and
  stated policy). In live mode (where in your working copy and on
  GitHub: your plan.md and its deviation notes, your branch's diff,
  your draft title and description, your captured test output, the
  repo's PR template and CONTRIBUTING.md).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("every changed file falls inside the
  plan's stated boundary or a deviation note") over adjectives ("the
  diff is clean").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts three ways: your
procedure says WHEN to gather each family, this guide says WHERE, and
your SKILL.md says the tool reads both. Write the map you wish your
executor had.
-->

## Plan fidelity (harness category: silent-drift)

**Where it lives.** Eval bundle: `## Plan context` (the plan's steps,
`Files:`, `Not in scope:`) on one side; `### Diff` and `### Description`
under `## Candidate PR` on the other. Live mode: `plan.md` (and its
Deviations section) and the posted plan comment on the issue, against
`git diff main...HEAD` and `pr_draft.md`.

**What good looks like.** Every changed file and behavior in the diff
is one the plan named, every planned step shows up in the diff, and any
difference is explained in the description. A description that claims
the plan was followed when the diff shows otherwise is drift.

## Test evidence (harness category: not-tested)

**Where it lives.** Eval bundle: `### Test evidence` under
`## Candidate PR`, read against the test plan in `## Plan context`.
Live mode: `test_evidence.md` (before/after output and `make` checks),
read against the test plan in `plan.md`.

**What good looks like.** Real output showing the bug before the fix
and showing it fixed after, matching what the test plan predicted, and
the repo's own checks shown with their outcome. A check that couldn't
run is fine if the PR says so and why. "Tests pass" with no output is
not evidence.

## Diff quality (harness category: unreviewable)

**Where it lives.** Eval bundle: `### Diff` and `### Commits` under
`## Candidate PR`. Live mode: `git diff main...HEAD` and
`git log main..HEAD` on the branch.

**What good looks like.** The diff only has the fix and its tests.
Tells of a bad diff: debug prints, commented-out code, dead code,
leftover draft files (plan.md, notes), reformatted or unrelated lines.

## Standards and comms (harness category: standards-wall)

**Where it lives.** Eval bundle: `## Repo facts` (the `pull requests:`
line for template and asks, the `contribution policy` line for AI
policy) and `## Thread highlights` (maintainer direction), read against
`### Title` and `### Description`. Live mode: the repo's
`.github/PULL_REQUEST_TEMPLATE.md`, `CONTRIBUTING.md`, and the issue
thread on GitHub, against `pr_draft.md`.

**What good looks like.** This family has three checks, one rule each:
- `repo-template`: every section and ask the repo states (template
  sections, issue link, changelog or other required entries) is present
  with real content. No template and no asks means nothing to fail.
- `ai-disclosure`: the PR meets the repo's AI policy as written,
  including any ask to name the tool and the extent. No stated AI policy
  means nothing to fail.
- `maintainer-direction`: every maintainer or collaborator direction in
  the thread is followed, or the PR says openly why not. No direction
  means nothing to fail.
