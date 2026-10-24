# Contribution: tools/ax25_replay.cpp: heap overflow in loadWav() when the WAV data chunk length is not a multiple of 4

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Bryant Nguyen
**GitHub Username:** bnguyen142
**Issue:** https://github.com/aethersdr/AetherSDR/issues/4907
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: issue scout tool**

I built a tool that searches and aids in narrowing down bugs that would be
candidates for me specifically. We had to change from the beets issue
because the scout flagged that beets' AI_POLICY.md bans AI on good first
issues.
Running the queries with no spacing between them caused GitHub to block the
last two, while Python got through because it ran first. We realized that
we need to space each query separately.

**Unit 6: Voice Guard**

The voice guard was extremely useful to prevent me from making errors.
However, I had to tweak it because of the clash between my "never post
a date" rule and the class guidance, which led me to reword line 79 of my
voice guide. I ran the manual check twice and the hook stopped the real post on a rule
the manual check missed. What I learned is the hook is critical; I
actually shared it with my coworkers since they ran into such issues
before.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I picked AetherSDR because it's one file and Linux-testable. Out of the
set, this was the appropriate item for me, and one I was comfortable with.

**Claim comment:** https://github.com/aethersdr/AetherSDR/issues/4907#issuecomment-6391036939 (posted 2026-10-24 16:46 PDT)

**Toolkit**

- Tool: issue-scout. The tool was helpful because it helped narrow down
  issues. The full scan was useful because it narrowed the field cheaply
  before running an expensive LLM to do the deeper check. This is a faster
  way to do it and reduces cost and time.
- What it produced: 3 graded: 2 accepts, 1 reject. It accepted AetherSDR
  #4907, with all 6 required checks passing.
- Difficulties: We hit the API rate limit because we tried to run all
  three queries close together. It took me at least 2 hours to realize
  what the actual issue was. We still need the deeper grading check to
  find subtle AI usage bans in particular situations.
- What changed: Pacing fixed the rate limit. The AI bans showed we need a
  rubric patch, and we applied a patch to fix it. We also added a
  no-bot-dispatch check.

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

[What a stranger would need to stand this up: your operating system,
the relevant versions (language, runtime, package manager, the project
itself), and how you set the project up. Then the challenges you faced
and how you solved them, or that none came up.]

**Steps to Reproduce**

1. [Step 1]
2. [Step 2]
3. [Observed result]

**Reproduction Evidence**

- **Commit showing reproduction:** [link to commit in your fork]
- **Screenshots/logs:** [if applicable]
- **My findings:** [what you discovered during reproduction; an
  honest, evidenced cannot-reproduce that led you to re-pick or ask
  the maintainer belongs here and costs you nothing]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**2026-10-24**

I filled out the voice guide today with the 5 missing Wrong/Right
examples for questions and pings. I hit a snag with conflicting
instructions: one from last class said to report back in a day or so,
however it conflicted with my never promise a date rule, which was
referring to fixing the bug and not each individual step. I reworded
the guard to allow dates for steps but not promise when the bug would
be fully resolved. The guard did catch some issues when I was claiming
the bug. One of the things I had in my guard was to not claim a bug
unless I run it. This led to some interesting findings, including that
a blocker was no longer a blocker. I also tested a fix and confirmed
that a change to one file stops the overflow. Also made the claim on
the bug (posted 16:46 PDT).

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
