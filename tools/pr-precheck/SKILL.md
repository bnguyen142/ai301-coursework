---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

<!--
THIS IS THE PART YOU WRITE, and it is the last one: the frame itself.
Weeks 1 through 3 handed you a working SKILL.md and you filled the
files behind it; this week the frame ships as headings, and you write
what it says. The frontmatter above and the section headings below are
fixed (CONTRACT.md's layout rule); the instructions under each heading
are yours. Write instructions to the tool, in the imperative, the way
weeks 1-3's frames spoke to you: what to read, in what order, what to
refuse, what to emit. Your executor in the rotation is the test: a
frame gap they hit (cannot tell what the tool reads, or how a verdict
gets assembled) is a missing sentence here.

One section is not yours: the JSON schema in "Verdict and output" is
reproduced from CONTRACT.md verbatim and may not be altered. Your
words decide everything around it.
-->

## The question

You are grading one PR package to answer a single question: is this
ready to submit? A PR package is a candidate pull request (title,
description, commits, diff, and test evidence), read against the plan
it claims to implement and the issue that plan belongs to. Grade
exactly one package per run. Do not answer a different question, and
do not answer from gut feel: answer by executing the components in
this directory.

## Inputs and modes

Run in exactly one of two modes:

- **Live mode**: the student's own PR before it goes out. Inputs:
  - the plan: `plan.md` with its Deviations section, and the plan
    comment posted on the issue;
  - the diff: everything the branch changes relative to the repo's
    default branch, from `git diff main...HEAD` (three dots) run in the
    student's working copy, plus `git log main..HEAD` for the commits;
  - the draft PR title and description: `pr_draft.md`;
  - the test evidence: `test_evidence.md`;
  - the issue, its thread, the repo's PR template
    (`.github/PULL_REQUEST_TEMPLATE.md`), and `CONTRIBUTING.md`,
    gathered from the real repo (via `gh` or the working copy).
  A house-chain student reads the house plan and house repro pack in
  place of their own plan and repro; the same checks grade the same
  things.
- **Eval mode**: a package bundle (a markdown file with repo facts, the
  issue, thread highlights, plan context, and the candidate PR) is the
  whole world. Use only the bundle text as evidence and fetch nothing.
  Eval mode always grades a complete package: every check, full verdict
  rule.

## The scope seam (live mode only)

In live mode, read `scope.md` in this skill directory before anything
else. Apply its house rules when reading evidence. Refuse to grade a PR
that does not target the repo on its `Repo:` line. If the `Repo:` line
still carries an unfilled placeholder, stop without grading and tell
the student to fill the `Repo:` line in `scope.md` with their section's
Path Review repo. Never guess a scope. In eval mode, ignore `scope.md`
entirely.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md`: the student's own rules for
how they write upstream. Hold the outgoing PR text, the title and the
description, against those rules, and report each rule the draft breaks
in the summary, quoting the rule. The voice guide never changes the
verdict on its own, because voice is personal and no rubric check reads
it. In eval mode, ignore `voice-guide.md` entirely.

## Component reads

- `rubric.md` is the brain: it defines the checks (name, evidence, pass
  condition, weight) and the verdict rule.
- `references/evidence-guide.md` is the map: where each evidence family
  lives in a PR package (and, live, in the working copy and on GitHub),
  and what good looks like there.
- `procedure.md` is the hands: execute it as written for read order,
  evidence gathering, check execution, and verdict assembly.

Where the procedure is silent on a step, report the gap in the summary;
do not improvise around it. If `rubric.md` has no checks filled in, or
`procedure.md` has no steps filled in, refuse to grade and say which one
is empty: this tool cannot grade without a rubric and a procedure.

## Verdict and output

The verdict space is binary: `accept` (ready to submit) or `reject`
(hold). There is no third verdict and no score; reservations go in
check evidence lines. Before the JSON you may show a short readable
summary (a line per check, plus any voice-guide notes in live mode).
End the reply with the fenced JSON block below, valid and last, with
nothing after it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote
  that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish: a terse complete PR can be ready and
  a confident, well-formatted one can hide drift. Read the PR itself
  against the plan, the issue, and the repo's stated standards.
- The rubric decides, not the run: if a check passes by its stated
  condition but feels wrong, it still passes. Note the tension in the
  summary; the fix belongs in the rubric.
- The procedure decides how, not the run: follow `procedure.md` as
  written and report its gaps instead of inventing steps.
- Treat `unclear` as the rubric's verdict rule directs. Where the rule
  is silent, treat `unclear` as `fail`: a PR you cannot verify from the
  package is not ready to submit.
