# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| matches-issue | The plan's stated problem and proposed change, read against the behavior the issue's title and body report | Pass if the plan sets out to fix the behavior the issue reports. A plan that also covers a second route to that same behavior, found in the repro, still passes. Fail if the plan fixes a different problem than the one reported. | required |
| evidence-supports-cause | The plan's stated cause, read against every step and result in the repro evidence, including control steps | Pass if the repro results point to the stated cause and no result contradicts it. Fail if any repro result shows the cause is somewhere else (for example, the bug still happens with the blamed part removed or bypassed). Also fail if the cause rests only on a thread comment or a reading of the code, with no repro result behind it. | required |
| test-proves-fix | The plan's test plan, read against the repro evidence's steps and actual results | Pass if the test re-runs the reproduction, or a step that observes the same behavior, and states a result after the fix that differs from what the repro shows today. Fail if the test would pass today, before any fix. "Run the test suite" or "nothing regresses" alone fails. | required |
| narrow-scope | The plan's list of files and changes, and anything it says is in or out of scope | Pass if the plan names the actual files it will touch and the change stays narrowly on the reported bug. Fail if the change spreads across several layers or a wide range of unrelated functions, or if it restructures or re-architects code beyond what fixing the bug needs. | required |
| maintainer-alignment | The plan comment and the plan's approach, read against the thread highlights (maintainer and collaborator statements) and the repo-facts block | Pass if the plan follows any direction a maintainer has given in the thread. A plan that disagrees with a maintainer still passes if it says so openly and states it will not build the disputed change until the maintainer agrees. Fail if the plan goes ahead with an approach a maintainer ruled out or argued against, without raising the disagreement. Also fail if the plan comment breaks a rule the repo states for contributions. AI disclosure: if the repo's policy asks for disclosure of AI use in comments, issues, or "any form", the plan comment must include one, and a comment without it fails. Do not require proof that AI was used, because every plan in this setting is written with AI help. A disclosure ask that covers only pull requests or code does not apply to a plan comment. | required |
| buildable | The plan's description of the issue, the fix, and the specific change, read against how complex the repro shows the bug to be | Pass if a stranger could start the work from the plan alone: it says what is wrong, what the fix is, and what will change (which file and function, and what is done there). A simple fix may be brief. A complicated or hard-to-follow fix must also cover the edge cases it has to handle. Fail if any of the three is missing or too vague to act on, such as "fix the handling" with no place or change named. Also fail if the plan and the plan comment describe different changes or contradict each other. | required |
| honest-unknowns | The plan's and comment's claims of certainty, read against what the repro evidence actually showed | Pass if the plan claims no more certainty than the repro supports, and names what it has not tested or does not know where that matters. Note it as a fail if the plan presents a guess or an untested case as settled. | preferred |

## Verdict rule

Accept only if every required check passes. A fail on any required
check means reject. Preferred checks are reported but never change the
verdict. A check graded unclear counts as fail, and the reason must say
what evidence was missing.
