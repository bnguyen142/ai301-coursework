# Rubric: is this pull request ready to submit?

<!--
THIS IS THE PART YOU WRITE (fourth week running; this is the rubric's
final form in the sandbox). Your frame in SKILL.md executes whatever
checks you define here, via your procedure.md. It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the diff read against the plan's scope, the test
     evidence read against the plan's test plan, the description read
     against the diff, the repo-facts block's template asks) or a
     location from your references/evidence-guide.md. "The PR" is not
     a source; "the diff's changed files read against the plan's
     stated boundary" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself
     (does the diff fall inside the plan plus its deviation notes? is
     the claimed evidence observable?), never the write-up's shape
     (how long the description is, how many commits there are).
     Structure-shaped checks are what make graders disagree with
     themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (submit) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. State the `unclear`
   treatment explicitly: the frame here is YOUR SKILL.md, so a rubric
   that stays silent is only covered if your frame's grading
   discipline says what happens (the contract's own default is that
   an unverifiable claim fails).

Cover what actually gets bad PRs submitted. The failure families the
lecture named ARE the harness's scoring categories, same names as the
eval README: silent drift (the diff silently does more or less than
the posted plan, or the description claims fidelity the diff
contradicts), not tested (the evidence proves nothing observable, or
the repo's own checks were never run), unreviewable (debris or
unrelated hunks bury the change), and standards wall (the repo's
stated template and disclosure asks are ignored). Your evidence
guide's four headings map onto these one to one (plan fidelity =
silent drift, test evidence = not tested, diff quality =
unreviewable, standards and comms = standards wall), and the category
floor is scored on exactly these names plus clear accept. A rubric
that ignores a category will fail the eval packages built around
that category. And remember the honest-outcome
rule, fourth week running: a PR that honestly discloses a shortfall
can be ready; a rubric that equates "less than everything" with
"hold" fails the set.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| plan-fidelity | The diff and the description, read against the plan context (its steps, files, and not-in-scope) | Pass if the diff does what the plan said it would do, or any difference is explained in the description. Fail if there's a difference and it isn't explained. A description that claims the plan was followed when the diff shows otherwise counts as unexplained. | required |
| test-evidence | The test evidence section, read against the plan's test plan and the repro steps; plus the outcome of the repo's own checks (lint, typecheck, test suite) | Pass if the evidence shows the bug before the fix and shows it fixed after, the way the plan's test plan said it would, and the repo's checks were run with their outcome shown. A check that couldn't run still passes if the PR says so and why. Fail if there's no proof, or it only says "tests pass" without showing output. | required |
| clean-diff | The diff and the commit list | Pass if the diff only has the fix and its tests. Fail if it has junk (debug prints, commented-out code, dead code, leftover draft files) or reformatted or unrelated lines that have nothing to do with the fix. | required |
| repo-template | The title and description, read against the repo-facts block's pull-request asks and contributing rules (PR template sections, issue link, changelog or other required entries) | Pass if every section and ask the repo states is present with real content, or the repo states none. Fail if a stated section or ask is missing, left empty, or filled with boilerplate. | required |
| ai-disclosure | The description, read against the repo-facts block's AI-use policy | Pass if the PR meets the repo's AI policy as written (for example, naming the tool and the extent when the policy asks for that), or the repo states no AI policy. Fail if the repo asks for disclosure and the PR has none, or gives less than the policy asks. | required |
| maintainer-direction | The PR's approach and description, read against every maintainer or collaborator statement in the thread highlights | Pass if the PR follows each maintainer direction, or says openly why it does not, or the thread has no maintainer direction. Fail if the PR goes against a maintainer direction without saying so. | required |

## Verdict rule

Accept only if every required check passes. A fail on any required
check means reject. Preferred checks are reported but never change the
verdict. A check graded unclear counts as fail, and the reason must say
what evidence was missing.
