# Toolkit Portfolio

Path: `beat-3-portfolio/toolkit-portfolio.md`

**Student:** Bryant Nguyen

## Featured Tools

These tools were the most helpful. The scout helped weed out a lot of tickets that weren't right for me and/or had various issues. It improved the signal to noise ratio so that I spent more time reading good candidate tickets. Ship-gate was very important in that it made sure I pushed code that fixes the issue and matches the PR description. Voice guard was very powerful in that it made sure I didn't promise things I can't know and made sure I backed my claims. PR-monitor is useful because it allowed me to focus on other things instead of constantly rechecking how the response on the PR is going.

The scout is mine, because I gave it criteria for what it should search for to consider as good candidates. For ship-gate, I determined the OS because I am using Linux. I also picked the checks to include, which were the Linux build from CI, my own ASan run, and static checks. I skipped the real FlexRadio test because it was not applicable, since `ax25_replay` is an offline, WAV-only tool with no radio I/O, and it was optional, not required. Voice guard matches the rules I created that made sure I didn't promise a finish date to fix the issue. For PR-monitor, I decide what is considered the correct time to nudge and when to walk away.

**issue-scout**

**What it is, and the judgment hole I wrote:** Issue-scout is essentially a way to gather appropriate bug tickets. The rubric decides whether an issue is good at all. My fit profile decides whether it's a good fit for me, which is found in scope.md.

**How it evolved, dated:** All three changes happened on the same day, 2026-10-18. The first was the rate-limit fix. I discovered that my scout was hitting its API back-to-back, which meant the second and later queries got rejected. The second was running across a bug ticket that had an issue-label-scoped ban: AI was banned on issues labeled "good first contribution," while the repo as a whole didn't ban AI. So I refined the scout to check not just the repo level, but also the individual issue. The third was that #4907 was marked bot-eligible on 9-29, so I added `no-bot-dispatch`, which fails at grading if labeled within 7 days, but #4907 still passes the check since the label was more than 7 days old on the 10-18 scan.

**Evidence:** The final tally was 3 graded, 2 accepts, 1 reject. The reject was beets#2491. The scout rejected beets even before the patch; however, that was only its own judgment. Under a stricter reading of the old wording, beets could have passed, so the patch now says a scoped ban fails, and every future run has to reject it.

What the scout said about beets on 10-18 (`scan-output.md`):

> **beetbox/beets#2491** (Python, the best match for the profile): sunk by
> **ai-policy-permits**. The repo's `AI_POLICY.md` says: *"AI is not for 'good
> first contribution' issues."* This issue has the `good first issue` label.
> The rubric's wording is about refusing AI across the repo, and this ban
> covers only one class of issue. Treated as a refusal for this issue.

What I added to the rubric because of it (`rubric.md` line 13, commit `bc1dd7d`, 2026-10-18):

> A scoped AI ban on the issue's label counts as a refusal.

**pr-precheck + ship-gate**

**What it is, and the judgment hole I wrote:** Ship-gate runs every check in my manifest before the PR opens and blocks it if one fails. The judgment I wrote is the required Linux build from the repo's CI files, plus my own ASan run, and excluding macOS/Windows.

**How it evolved, dated:** It evolved in two different and important ways. On 2026-10-31, the ship-gate's manifest caught 2 bugs in my own commands. A literal `<wav>` placeholder was left in a shell command, which gave an "unexpected EOF" error. The second bug caught was a `|` pipe character inside a markdown table cell, which broke the table parsing, as it was read as a new column. The second way was that the ship-gate's `pr-precheck` pass rejected my PR body twice, which made me correct it, using `--body-file` with the repo's PR template.

**Evidence:**

The pipe-free fix, as it stands today in `ship-manifest.md` (a local working
file, never committed — it's listed in `.git/info/exclude` by design):

```
... > /tmp/asan_run.txt 2>&1 ; ! grep -q "ERROR: AddressSanitizer" /tmp/asan_run.txt
```

The two `pr-precheck` rejections, same day (2026-10-31):

1. Malformed body: `gh pr create` used a bash heredoc (`--body "$(cat <<'EOF' ...)"`)
   that didn't evaluate into plain text — the body literally began with
   `$(cat <<'EOF'` — and was missing the template's required Intent,
   Constitution principle, Test plan, and Checklist sections. Fixed by writing
   the body to a file and using `--body-file`.
2. Unverified claim: `plan-fidelity [fail]` — the description said the sibling
   bug was "flagged on the issue," but none of the issue's comments actually said
   so yet. Fixed by rewording it honestly, then actually posting the comment.
   The posted comment went in PR #6902, 10-31 12:15Z, six minutes after the PR
   opened.

**voice-guard**

**What it is, and the judgment hole I wrote:** Voice guard made sure what I posted on GitHub was correct, which came from rules I wrote in voice-guide.md. It checked any statements I made and made sure I didn't assume. It also made sure not to overpromise completion of the entire PR bug fix.

**How it evolved, dated:** On 2026-10-24, the voice guide caught a conflict between my "never post a date" rule and class guidance, which said report back in a day or so. I had to reword it to "Promise the work, never the result or the finish date... a step date is fine." The second evolution, on 2026-11-01, was adding a new rule to re-read the entire thread before drafting a question. This was due to a follow-up draft that nearly re-asked Ozy311 something he had already answered.

**Evidence:**

The guard log on 10-24, where the guard caught a claim I inferred instead of ran (`guard-log.md`, rule text trimmed):

```
2026-10-24 15:49 [manual] stop: rule broken: "Every claim I post has to be something I ran, not something I inferred from reading the code. ..." | target: claim-draft.md
2026-10-24 16:34 [hook] stop: rule broken: "What I am deliberately not changing gets said out loud too, ..." | target: gh issue comment 4907 -R
2026-10-24 16:43 [manual] pass | target: claim-draft.md
2026-10-24 16:43 [hook] pass | target: gh issue comment 4907 -R
2026-10-24 17:25 [hook] pass | target: gh issue comment 4907 -R
```

Rule reworded (2026-10-24), from "Promise the work, never the result or the date" to "Promise the work, never the result or the finish date":

> "I commit to what I am going to look at, not to when I will be done or what I
> will deliver... Never promise when the finish date will be, but a step date
> is fine (for example, when I will post my reproduction results)."

New rule added (2026-11-01), "groundwork first":

> "Before drafting a question, re-read the whole thread, not just my own last
> comment — if a maintainer or reviewer already said how they want something
> handled, that's not an open question anymore, it's a decision I should
> follow."

**PR-monitor**

**What it is, and the judgment hole I wrote:** The PR-monitor checks the PR's state when I run it, for any response to my last post. The playbook is a set of rules for when to nudge and when to drop the PR. In addition, the playbook also covers response rules for review feedback. The rule I picked was 6 days before a nudge. I based it on other people's merged PRs directly in the same repo. It was based on 30 merged PRs from this repo, with 2 taking about 3.5 days. 20 out of 30 were within an hour. I picked 6 days to add buffer to be sure.

**How it evolved, dated:** Since this tool was recent, it barely changed. Partly because by the time I implemented this tool, my PR was already merged, and I last ran it on 2026-11-02.

**Evidence:**

`run-report.md`, 2026-11-02 07:33:

```
- PR: https://github.com/aethersdr/AetherSDR/pull/6902
- checks: 5 passing
- review decision: APPROVED
- state: MERGED: the PR is merged
```

I reviewed the drafted comment against my playbook and found nothing to
change: `drafted-move.md` (what the tool drafted) and the text I actually
posted to GitHub are identical text.

## Inventory

- repro-check: used to verify the bug before fixing it; built in Unit 2, not run on #4907.
- plan-check: used to verify the fix plan before building it; built in Unit 3, not run on #4907.

## The Arc

**Beat 1**
Date: 2026-10-09
Friction: I tried to bundle multiple different things into a single pass/fail check in pr-precheck (the `repo-standards` check). From grader feedback, it was noted that I was trying to do too much at once.
Change: I separated the check into three parts: repo-template, ai-disclosure, and maintainer-direction.
Result: The eval score changed from 20/20 down to 19/20. I chose to keep test-evidence strict rather than loosening it back to 20/20, to maintain accuracy.

**Beat 2**
Date: 2026-10-18
Friction: With no explicit instruction, the scout would send requests back to back, hitting GitHub's rate limit. The first request succeeded, followed by rejection of the subsequent requests in that session.
Change: I added a Pacing section with 30 seconds between each request.
Result: The re-run had 231 results and no block.

**Beat 3**
Date: 2026-10-18
Friction: One of the surprises I got when I opened a ticket was a ticket that explicitly banned AI. This was a surprise since I had a criteria of looking for non-AI-ban bug tickets. Upon further inspection, I realized that my tool was looking for a ban on the entire repo and was not looking at each individual bug ticket. This particular bug ticket had an AI ban for beginner-type issues.
Change: I added an additional check to look for an AI ban at the individual issue level, not just the repo level.
Result: The eval re-run was 20/20 with no flips, so the patch broke nothing.

**Beat 4**
Date: 2026-10-18
Friction: When I tested the scope-bounded to scope-feasible patch, issue-20 flipped from a correct reject to an incorrect accept — the old check had gotten it right.
Change: I considered adding a human-filed check to catch this case.
Result: none — I never implemented it.

**Beat 5**
Date: 2026-10-24
Friction: The voice guide caught a conflict between my "never post a date" rule and class guidance, which said report back in a day or so.
Change: I had to reword it to "Promise the work, never the result or the finish date... a step date is fine."
Result: I tested it with two drafts: one with a step date, which passed, and one with a finish date, which failed.

**Beat 6**
Date: 2026-10-31
Friction: The ship-gate's manifest caught 2 bugs in my own commands: a literal `<wav>` placeholder left in a shell command (gave an "unexpected EOF" error), and a `|` pipe character inside a markdown table cell that broke the table parsing.
Change: I fixed the command to point to the real WAV files with an absolute path, and rewrote the ASan check to avoid the pipe.
Result: After the fix, all 3 manifest checks (Linux build, ASan regression, 4 static-check scripts) passed clean.

**Beat 7**
Date: 2026-11-01
Friction: A follow-up draft nearly re-asked Ozy311 something he had already answered.
Change: I added a new rule to re-read the entire thread before drafting a question.
Result: none — since adding the rule, I haven't been able to test it, since there wasn't any question to ask once the ticket was merged.

**Beat 8**
Date: 2026-11-02
Friction: I had been manually rechecking the PR's status instead of having a tool do it for me.
Change: I ran the PR-monitor's full run, which classified the PR as merged and drafted a close-the-loop comment via my playbook.
Result: I reviewed the draft against my playbook, found nothing to change, and posted it as-is — crediting rfoust and Ozy311 by name.

## Limitations and What's Next

If you use `!` in Claude chat like I did on 10-24, it will bypass the safety. This isn't fixable by improving the hook — `!` is a different execution path that hooks can't see at all. It had passed in the terminal at 16:43, before I posted. Realizing my error, I re-ran the hook at 17:25 to make sure it properly passed. The lesson I learned here was when something blocks me, fix the input and not the path. So in this case, running the voice guard before posting is the correct move. It is an inherent risk that cannot be overstated; what happened to me on 10-24 is the evidence here (see the 16:43 and 17:25 lines in voice-guard's evidence).

As I use these tools, they will keep evolving as I learn more nuances and hit interesting scenarios. Next, I would change the pr-precheck test-evidence check: when the regression check is manual, the tool should check if a small extraction would make it testable. The key example and proof was rfoust's extraction. I would also add a human-filed check, because it would catch tickets written by a bot or AI.

**Tool limits.**

issue-scout: no way to check if the issue was already fixed upstream (the example is docling #4609), no fork check, and newest-first ordering favors issues others grab first.
pr-precheck + ship-gate: it only confirms every item on my list; however, it didn't tell me if the list was good enough. The example here was that a manual ASan run was listed as the regression test; however, later on a reviewer moved `loadWav()` and wrote a real test for it, `ax25_replay_wav_test`.
voice-guard: the `!` bypass described above — it can't see or guard commands run that way.
PR-monitor: only the MERGED path has actually been exercised. The feedback-in, branch-stale, and quiet/nudge forms are untested.
