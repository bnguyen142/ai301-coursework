# Procedure: how this skill grades a plan package

<!--
THIS IS THE PART YOU WRITE, and it is a new kind of part. Weeks 1 and
2, SKILL.md carried a numbered workflow and you only wrote judgment
files. This week the workflow is gone from the frame: SKILL.md says
"execute procedure.md", and these are the operating steps you author.
The machinery is in your hands now.

Your operator swap is the design brief. When your executor stalled
because your rubric said WHAT to decide but not HOW to find the
evidence, that was a procedure gap. This file is where those gaps get
closed: a complete procedure lets someone who has never seen a plan
package before (a groupmate, or the skill itself) grade one exactly the
way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
repro evidence before the plan, and note what behavior it pins down"
is a step; "understand the context" is a wish.
-->

## Read order

1. Read the issue (title, body) first. Note in one line what behavior is
   reported as the bug.
2. Read what the plan says the problem is: its stated root cause and
   where it puts it. Note the claim before looking at the evidence, so
   the evidence is read as a test of that claim.
3. Read the repro evidence: every step, input and result, including
   control steps. Note what each result shows and what changed between
   steps.
4. Read the rest of the plan: the fix, the files and changes, the scope
   statement, and the test plan.
5. Read the thread highlights and the repo-facts block, then the plan
   comment. Note every maintainer or collaborator statement about the
   approach, and every rule the repo states for contributions.

## Evidence gathering

Before grading, write down these facts, each pulled from the package
part named in `references/evidence-guide.md`:

- **The reported bug:** the behavior from the issue (read order step 1).
- **The claimed root cause:** quoted from the plan.
- **The claimed fix:** what the plan says it will change and where
  (file and function), quoted.
- **What the repro results show:** each step's result in one line.
- **Alternate explanations:** for the repro results as given, list any
  other cause that would explain them just as well as the plan's
  claimed cause. Note which repro step, if any, rules each one out.
- **The test plan's expected result after the fix**, quoted, next to
  what the repro shows today.
- **Scope:** the files named, and anything said to be in or out of
  scope.
- **Maintainer direction and repo rules:** each statement or rule
  quoted (read order step 5).
- **Certainty claims:** any place the plan or comment states something
  as settled that the repro did not test.

## Check execution

1. Run the checks in the order they appear in `rubric.md`, applying
   each check's pass condition exactly as written there, to the facts
   gathered above. Re-read a package part only if a gathered fact is
   missing.
2. When the evidence a check needs is missing or too vague to decide
   on, grade the check `unclear`. Say what is missing and what test or
   step would settle it. Do not fill the gap by guessing or by
   reasoning from how the code probably works. This skill cannot run
   new tests itself.
3. When a repro result points the opposite way from the plan's claim,
   record that result, quoted, as the reason for the check's grade.
4. Grade what the plan actually says, not how it is laid out. Missing
   headings or a short plan are not reasons for a grade.

## Verdict assembly

1. Apply the verdict rule in `rubric.md` to the check grades. Do not
   restate or change it here.
2. For a reject, quote the first required check (in rubric order) that
   failed or was unclear, plus the package text that decided it.
3. For an accept, every required check passed. List any preferred
   check that failed as a note, without changing the verdict.
