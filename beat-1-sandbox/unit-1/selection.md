# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72

**Verdict output**

````
**Issue #72** — `verify_password` raises `UnknownHashError` on malformed stored hashes instead of returning False (codepath/pathreview-ai301-fa26-s3). In scope: it's in the course Path Review repo.

**Required checks**

- **repo-not-archived — pass.** `"isArchived": false`; no archive banner.
- **commits-recent — pass.** All 5 latest `main` commits are human-authored by `Aburke225` and dated 2026-09-16 (x3) and 2026-08-24 (x2) — 5 of 5 within 90 days of today (2026-09-20).
- **ai-policy-permits — pass.** No `AI_POLICY.md`, no root/`.github` CONTRIBUTING; `docs/CONTRIBUTING.md` covers branch naming, commits, CI, and xfail removal but says nothing about AI-assisted work. Silence passes.
- **unclaimed — pass.** `assignees: []`, no linked or cross-referenced PRs in the timeline, and the thread has zero comments — no claim to be stale or standing.
- **scope-bounded — pass.** One concrete misbehavior with a stated end state ("Verification against a malformed hash should fail closed (return `False`), not raise"), two named files, a covering `xfail` test to unmark, filed by a COLLABORATOR, labeled `good first issue` / `tier-1`, estimated 1-2 hours. Not an umbrella, no design argument, no abandoned PRs.

**Preferred checks**

- **maintainer-responsive — pass.** Collaborator `Aburke225` replied on #43 and #52 on 2026-09-16, six days after those issues opened (2026-09-10) — well inside 30 days.
- **shipped-recently — pass.** `latestRelease: null`; the rubric says no releases at all doesn't fail this, and `commits-recent` carries liveness.

**Verdict: accept.** Every required check passes and both preferred checks pass too, so it ranks well against other accepted candidates. On fit: this is squarely your profile — Python with a pytest suite, expected behavior stated plainly, and a `@pytest.mark.xfail(strict=True)` test that tells you exactly when you're done (and that you must delete the marker, or CI fails on `XPASS(strict)`). No front-end work, no unmade product decision, no thread to excavate.

One note on the house rule: the empty thread means nobody has claimed it yet, but claim anyway — course credit attaches to the PR you open, and classmates' claims wouldn't block you regardless.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72",
  "checks": [
    {"name": "repo-not-archived", "grade": "pass",
     "evidence": "gh repo view reports \"isArchived\": false; no archive banner on the repo."},
    {"name": "commits-recent", "grade": "pass",
     "evidence": "5 of 5 latest main commits are human (Aburke225), dated 2026-09-16 x3 and 2026-08-24 x2, all within 90 days of 2026-09-20."},
    {"name": "ai-policy-permits", "grade": "pass",
     "evidence": "No AI_POLICY.md and no AI clause in docs/CONTRIBUTING.md or the README; silence passes."},
    {"name": "unclaimed", "grade": "pass",
     "evidence": "assignees: [], timelineItems for linked/cross-referenced PRs returned [], and the issue has zero comments."},
    {"name": "scope-bounded", "grade": "pass",
     "evidence": "\"Verification against a malformed hash should fail closed (return `False`), not raise\" — one defect, two named files, an xfail test to remove, 1-2 hour estimate, filed by a COLLABORATOR."},
    {"name": "maintainer-responsive", "grade": "pass",
     "evidence": "COLLABORATOR Aburke225 commented on #43 and #52 on 2026-09-16, six days after those issues opened on 2026-09-10."},
    {"name": "shipped-recently", "grade": "pass",
     "evidence": "latestRelease is null — no releases published at all, which the rubric states does not fail this check."}
  ],
  "verdict": "accept"
}
```
````

---

## Eval iterations

**Run history**

Five runs, in order:

1. `3/3` — smoke run, `--limit 3`, to confirm the harness and my installed rubric were wired together before spending on a full run.
2. `17/20` — first full run. Below the bar. Three disagreements: issue-04 and issue-19 rejected against a gold label of accept, both on `scope-bounded`; issue-15 accepted against a gold label of reject.
3. `5/5` — partial `--only` run over issue-04, issue-15 and issue-19 after revising `scope-bounded`, plus issue-01 and issue-09 as canaries to confirm the revision had not broken cases that were already passing.
4. `20/20` — full run confirming the revised rubric. Bar passed, category floor met.
5. `20/20` — full run after rewording the `commits-recent` pass condition. This is the run committed as `eval-run.txt`; its agreement line reads `20/20 scored items  (bar: 18/20: PASS)`.

Runs 1 and 3 were partial and could not have written the run file; runs 2, 4 and 5 were complete.

**Issue analysis**

`issue-04` (zxcalc/zxlive#555). My rubric returns **accept**; the gold label is **accept**; they agree.

They did not agree on my first full run, and how that changed is the substance of this unit for me. In run 2 my rubric returned **reject**, failing the issue on `scope-bounded`. The reasoning it produced was:

> Body reads only "Including remove identity, fuse spiders, remove self loops, etc." — the "etc." leaves the set of missing rule previews open-ended with no bounded end state

That reading follows directly from how I had written the check. I listed "lists sub-items meant to ship as separate pull requests" as a disqualifier and said nothing about how to read a list of examples, so an open-ended "etc." looked exactly like an unbounded set of work items. The same wording rejected issue-19 for listing a maintainer's suspected causes.

The gold label is accept because those are examples of one defect, not separate pieces of work: a maintainer filed one bug about missing rule previews and named a few instances of it. The size of the work asked for is one fix; only the writeup is open-ended. So the fault was in my check, not in the label. I added three explicit carve-outs — several edits delivering one coherent change, a list of examples of the same defect including a trailing "etc.", and a maintainer's list of suspected causes or optional follow-ups — and the issue now passes for the right reason.

**Check rationale**

The check, as it currently stands in `tools/issue-select/rubric.md`:

`commits-recent` — *Evidence:* "The dates of the five entries under 'last 5 default-branch commits' in the repo-facts block, compared against the capture date. Live: the commit list reached from the commit count on the repo front page, compared against today." *Pass condition:* "Count the commits among the 5 listed that are dated within 90 days before the capture date — in live mode, within 90 days before today. Ignore any commit authored by a bot account unless that commit merges a human's pull request. Pass if the count is 2 or more." *Weight:* required.

It is `required` because liveness gates everything else: in a repo nobody commits to, the quality of the issue does not matter, because nobody will read the pull request. It rejects issue-02, issue-07 and issue-17 on that basis.

It reads commits rather than releases because releases cannot carry this weight. issue-06 has "latest release: none published" and is a gold accept — its liveness is visible only in the commit stream. That is also why `shipped-recently` is preferred rather than required.

The threshold is 2 rather than 1 because a single recent commit can be noise: a typo fix, a badge update, an automated bump. Two suggests a pattern rather than a stray keystroke. The bot clause exists for the same reason in reverse — a repo kept warm by dependabot alone should not read as alive.

I rewrote the pass condition during this unit. It originally read "At least 2 of the 5 most recent default-branch commits are dated within 90 days," which never said 90 days *before what*. The anchor lived only in the Evidence column, so anyone reading the pass condition on its own could measure against today instead of the capture date and grade the same bundle differently. The current wording is three instructions — count, exclude, compare — and states the anchor inside the condition itself, because next unit a classmate runs this rubric exactly as written without asking me anything.

**Trade-offs**

My `commits-recent` check grades kubernetes/minikube (issue-13) as not alive, and it is wrong about that. Four of the five sampled commits are `kubernetes-prow[bot]` merging `minikube-bot`'s own branches — a yearly leaderboard update, an automatic nerdctl version bump, generated docs — and my bot clause discounts all four. Only #23409, merging a4abdul7's pull request, counts as human, so the count is 1 against a threshold of 2 and the check fails. The repo is plainly alive: 32k stars, and a maintainer filed that very issue the day before the capture date.

It cost nothing on this run, because issue-13 also fails `unclaimed` on two open linked pull requests and the gold label is reject either way. I accept the miss. The bot clause is still worth keeping — it is what stops a repo kept warm by dependabot alone from reading as active — but a five-commit window is too small a sample in a repo whose automation commits more often than its humans. Before using this rubric on real repositories I would widen the sample rather than drop the clause.

Worth recording alongside that: the 20/20 did not validate my numbers. Every repo in this set had either pushed within about three weeks of capture or been silent for over a year, so nothing sat near the boundary. A threshold of 1, 2 or 3, and a window of 90 or 180 days, would all have produced the same score. The eval proved the check separates alive from dead; it never tested where the line sits.

---

## Selection rationale

**Selection rationale**

**1. Fit and time.** This is Python with a pytest suite, and tests are my strongest ground — reading a failing test and working backwards from the failure to the cause is the part of this work I am genuinely good at. What decided it was the blast radius: the issue names its two files outright, `core/security.py` and `tests/unit/test_security.py`, so the bug is either in the function or in the test covering it, and nothing else in the codebase is in play. I can hold that much in my head without first learning how the rest of the application fits together, and a bounded surface like that is what makes the stated 1-2 hour estimate believable rather than optimistic. It is also the opposite of what I wanted to avoid: nothing here reaches across several modules or waits on an architectural decision.

**2. What the verdict got right, and what I weighed on top of it.** The rubric confirmed the things I would otherwise have checked by hand: the repository is not archived, all five recent commits are human and within 90 days, nothing in the contribution docs restricts AI-assisted work, there is no assignee or linked pull request, and the scope is one concrete misbehavior with a stated end state. Both preferred checks passed as well, which is why it ranked first among the three candidates I graded.

What the rubric could not see is that the issue ships its own done-signal. The covering test is already written and already failing under `@pytest.mark.xfail` with manifest id H-05, and because the marker is strict the suite turns red on `XPASS` the moment the fix works — the issue tells me to delete the marker as part of the fix. So the test itself tells me when I am finished, which is exactly how I like to work. The other two issues I graded, #61 and #62, are health-check bugs whose symptom I had already run into during AI201, and that familiarity was tempting, but a failing test I can run directly beat it.

**3. Claiming it.** I do not expect difficulty. On GitHub the issue shows no assignee, no linked pull request and an empty comment thread, so there is nothing formal to work around, though I know from grading it that other students in the section are already working on it in their forks. The Path Review house rule covers that: classmates' claims are normal here, several on one issue is expected, and credit attaches to the pull request I open rather than to whether mine is the one that merges. I am not commenting yet, because choosing is not claiming — the claim comment belongs in Unit 2, after the voice guide. The real dynamic is convergence rather than conflict: this is a tier-1 `good first issue` with a clearly stated fix, which is exactly what everyone else is filtering for, so I would rather post my claim early next week than late.
