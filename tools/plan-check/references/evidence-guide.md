# Evidence guide: where evidence lives in a plan package

<!--
THIS IS THE PART YOU WRITE (second week running: the judgment files
stay in your hands). The skill uses this guide as its map: for every
kind of evidence a rubric check names, this file says WHERE to find it
in a plan package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repro-evidence block,
  the candidate plan's scope statement or test plan, the plan comment,
  the repo-facts block). In live mode (where on GitHub or in the
  draft: the issue thread, the student's posted repro comment, the
  repo's docs, the draft plan and comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the stated cause cites behavior the
  repro evidence actually shows") over adjectives ("diagnosis is
  solid").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts twice: your
procedure.md tells the skill WHEN to gather each family, and this
guide tells it WHERE. Write the map you wish your executor had.
-->

Pass conditions live only in `rubric.md`; this guide says where to look.

## Diagnosis and grounding

Used by: `matches-issue`, `evidence-supports-cause`.

- **The reported bug:** the `## Issue` section (title and body).
- **The claimed root cause:** the `## Candidate plan` section, wherever
  the plan states its cause. Some plans put it under a `### Diagnosis`
  or `### Background` heading, and some have no headings at all.
- **What the bug actually does:** the `## Repro evidence` section: its
  steps, inputs, results and any control steps.
- **Alternate possible causes:** the `## Thread highlights` section.
  Commenters, reporters and maintainers often suggest other causes
  there, and some point to related issues.
- Live: the issue page on GitHub, the author's posted repro comment,
  and the draft `plan.md`.

## Scope

Used by: `narrow-scope`.

- **What the plan will change:** the `## Candidate plan` section,
  wherever it lists files and changes (headings vary: `### Scope`,
  `### Files`, `### Files and areas`, `### Proposed changes`,
  `### Approach`, or none).
- **More detail on the change:** the `## Candidate plan comment`
  section, which often restates the scope in different words.
- Live: the draft `plan.md` and `comment.md`.

## Executability

Used by: `buildable`.

- **The issue, the fix and the specific change:** the
  `## Candidate plan` section, wherever it names the file, the function
  and what will be done there. Read the `## Candidate plan comment`
  section for further detail.
- **How complex the bug is:** the `## Repro evidence` section (how many
  steps, inputs and conditions it took to trigger the bug).
- Live: the draft `plan.md` and `comment.md`.

## Test plan

Used by: `test-proves-fix`.

- **How success will be observed:** the `## Candidate plan` section,
  usually under `### Test plan`, sometimes inline.
- **What it must be compared with:** the `## Repro evidence` section's
  steps and actual results. That is what the bug looks like today.
- Live: the test-plan part of `plan.md`, compared with the author's
  posted repro comment.

## Honesty

Used by: `honest-unknowns` (preferred).

- **Claims of certainty:** anywhere in the `## Candidate plan` and
  `## Candidate plan comment` sections, compared with what the
  `## Repro evidence` section actually tested.
- **Stated unknowns and risks:** wherever the plan lists them, under
  any heading or none.
- Live: the draft `plan.md` (its risks and deviations parts) and
  `comment.md`.

## Comms

Used by: `maintainer-alignment`.

- **Maintainer direction:** the `## Thread highlights` section. Note
  who is a maintainer, owner or collaborator, and what they said
  about the approach.
- **Repo rules:** the `## Repo facts` section: contribution guidelines,
  templates and policies (including any AI-use disclosure rule).
- **What the author will post:** the `## Candidate plan comment`
  section, read alongside the `## Candidate plan` section.
- Live: the issue thread on GitHub, the repo's `CONTRIBUTING` and
  related docs, and the draft `comment.md`.
