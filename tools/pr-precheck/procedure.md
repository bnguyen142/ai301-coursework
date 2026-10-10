# Procedure: how this tool grades a PR package

<!--
THIS IS THE PART YOU WRITE (second week running for the procedure).
Week 3 you wrote these steps for a plan package; this week the graded
object is a PR package, and the read that matters most is a
side-by-side: the diff against the plan, the evidence against the test
plan, the description against both. Your week-3 procedure is the
pattern; do not paste it unchanged, because its read order was built
for a different object.

Your rotation is the design brief again, and this week friction routes
three ways: a stall on WHAT to decide is a rubric gap, a stall on
WHERE to look is a procedure gap (this file), and a stall on what the
tool even reads or outputs is a frame gap (your SKILL.md). A complete
procedure lets someone who has never seen a PR package before grade
one exactly the way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
plan's scope pair before opening the diff, and list the files the plan
names" is a step; "understand the change" is a wish.
-->

## Read order

1. Read the issue title and body. Note in one line the behavior it
   reports as the bug.
2. Read the plan (`## Plan context`; live: `plan.md` and the posted plan
   comment) before anything in the PR. Note its steps, the files it
   names, what it puts out of scope, its test plan, and any deviation
   notes. The plan is the yardstick: you need it first to tell whether
   each change in the diff belongs or is extra.
3. Read the diff and the commit list (`### Diff`, `### Commits`; live:
   `git diff main...HEAD`, `git log main..HEAD`). Note every changed file
   and what each hunk does.
4. Read the test evidence (`### Test evidence`; live: `test_evidence.md`).
   Note each command, its before and after output, and which repo
   checks were run and their outcome.
5. Read the title and description. Note every claim they make about
   what changed, what was tested, and whether the plan held.
6. Read the repo facts and the thread highlights. Note the PR template
   ask, any contribution rule, the AI-use policy, and every maintainer
   statement about the approach.

## Evidence gathering

Before grading, write down these pairings, each pulled from the
package part named in `references/evidence-guide.md`:

- **Diff vs plan:** each changed file and behavior, marked as named in
  the plan, explained in the description, or neither. Each planned step,
  marked as present in the diff or missing.
- **Description vs diff:** each claim in the description, marked as
  matching the diff or contradicted by it, with the contradicting hunk.
- **Evidence vs test plan:** the test plan's expected result after the
  fix, quoted, next to the evidence's actual before and after output.
  List the repo checks (lint, typecheck, tests) with their shown outcome,
  or "not shown", and any reason the PR gives for a check not running.
- **Debris:** any debug print, commented-out code, dead code, draft or
  notes file, reformatted line, or hunk unrelated to the fix, quoted.
- **Repo asks vs PR:** each template section, issue-link ask, AI
  disclosure ask, and maintainer direction, marked as honored, ignored,
  or not asked by this repo.

## Check execution

1. Run the checks in the order they appear in `rubric.md`, applying
   each check's pass condition exactly as written there to the facts
   gathered above. Re-read a package part only if a gathered fact is
   missing.
2. A shortfall the PR discloses with a reason is not a fail by itself.
   Grade it by whether the rubric's pass condition allows an explained
   difference, not by whether the PR did everything.
3. When the evidence a check needs is missing or too vague to decide
   on, grade the check `unclear` and say what is missing. Do not fill the
   gap by guessing how the code probably behaves. This tool does not run
   tests itself.
4. Grade what the PR actually does and shows, not how it is laid out. A
   short description or few commits are not reasons for a grade.

## Verdict assembly

1. Apply the verdict rule in `rubric.md` to the check grades. Do not
   restate or change it here.
2. For a reject, quote the first required check (in rubric order) that
   failed or was unclear, plus the package text that decided it.
3. For an accept, every required check passed. List any preferred check
   that failed as a note, without changing the verdict.
