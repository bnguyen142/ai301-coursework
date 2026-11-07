# Toolkit Portfolio

Path: `beat-3-portfolio/toolkit-portfolio.md`

**Student:** Bryant Nguyen

## Featured Tools

These tools were the most helpful. The scout helped weed out a lot of tickets that weren't right for me and/or had various issues. It improved the signal to noise ratio so that I spent more time reading good candidate tickets. Ship-gate was very important in that it made sure I pushed code that fixes the issue and matches the response. Voice guard was very powerful in that it made sure I didn't promise things I can't know and made sure I backed my claims. PR-monitor is useful because it allowed me to focus on other things instead of constantly rechecking how the response on the PR is going.

**issue-scout**

**What it is, and the judgment hole I wrote:** Issue-scout is essentially a way to gather appropriate bug tickets. The rubric decides whether an issue is good at all. My fit profile decides whether it's a good fit for me, which is found in scope.md.

**How it evolved, dated:** All three changes happened on the same day, 2026-10-18. The first was the rate-limit fix. I discovered that my scout was hitting its API back-to-back, which meant the second and later queries got rejected. The second was running across a bug ticket that had an issue-label-scoped ban: AI was banned on issues labeled "good first contribution," while the repo as a whole didn't ban AI. So I refined the scout to check not just the repo level, but also the individual issue. The third was that #4907 was marked bot-eligible on 9-29, so I added `no-bot-dispatch`, which should not show recently bot-assigned bug tickets.

**Evidence:** The final tally was 3 graded, 2 accepts, 1 reject. The reject was beets#2491. The scout rejected beets even before the patch; however, that was only its own judgment. Under a stricter reading of the old wording, beets could have passed, so the patch now says a scoped ban fails, and every future run has to reject it.

What the scout said about beets on 10-18 (`scan-output.md`):

> **beetbox/beets#2491** (Python, the best match for the profile): sunk by
> **ai-policy-permits**. The repo's `AI_POLICY.md` says: *"AI is not for 'good
> first contribution' issues."* This issue has the `good first issue` label.
> The rubric's wording is about refusing AI across the repo, and this ban
> covers only one class of issue. Treated as a refusal for this issue.

What I added to the rubric because of it (`rubric.md` line 13, commit `bc1dd7d`, 2026-10-18):

> A scoped AI ban on the issue's label counts as a refusal.

**ship-gate**

**What it is, and the judgment hole I wrote:** This tool helped when shipping code. The manifest makes sure what I say I am shipping matches the actual code and tests (as appropriate). It made sure that I was delivering what I said I was doing.

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

**voice-guard**

**What it is, and the judgment hole I wrote:** Voice guard made sure what I posted in the PR was correct, which came from rules I wrote in voice-guide.md. It checked any statements I made and made sure I didn't assume. It also made sure not to overpromise completion of the entire PR bug fix.

**How it evolved, dated:** On 2026-10-24, the voice guide caught a conflict between my "never post a date" rule and class guidance, which said report back in a day or so. I had to reword it to "Promise the work, never the result or the finish date... a step date is fine." The second evolution, on 2026-11-01, was adding a new rule to re-read the entire thread before drafting a question. This was due to a follow-up draft that nearly re-asked Ozy311 something he had already answered.

**Evidence:**

The guard log on 10-24, where the guard caught me saying I did something when I didn't (`guard-log.md`, rule text trimmed):

```
2026-10-24 15:49 [manual] stop: rule broken: "Every claim I post has to be something I ran, not something I inferred from reading the code. ..." | target: claim-draft.md
2026-10-24 16:34 [hook] stop: rule broken: "What I am deliberately not changing gets said out loud too, ..." | target: gh issue comment 4907 -R
2026-10-24 16:43 [hook] pass | target: gh issue comment 4907 -R
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

**What it is, and the judgment hole I wrote:** The PR-monitor checks the PR's state when I run it, for any response to my last post. The playbook is a set of rules for when to nudge and when to drop the PR. In addition, the playbook also covers response rules for review feedback.

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
posted to GitHub are byte-identical.

## Inventory

- repro-check: used to verify the bug before fixing it.
- plan-check: used to verify the fix plan before building it.

## The Arc

**Beat 1**
Date: 2026-10-09
Friction: I tried to bundle multiple different things into a single pass/fail check in pr-precheck (the `repo-standards` check). From grader feedback, it was noted that I was trying to do too much at once.
Change: I separated the check into three parts: repo-template, ai-disclosure, and maintainer-direction.
Result: The eval score changed from 20/20 down to 19/20. I chose to keep test-evidence strict rather than loosening it back to 20/20, to maintain accuracy.

**Beat 2**
Date: 2026-10-18
Friction: With no explicit instruction, the scout would send requests back to back, hitting GitHub's rate limit. This led to waiting about an hour each time — the first request succeeded, followed by rejection of the subsequent requests in that session.
Change: Looking closer, I realized I needed to space out each request by a certain amount of time to prevent requests from being rejected.
Result: After I made the change, I didn't hit the rate limit issue anymore.

**Beat 3**
Date: 2026-10-18
Friction: One of the surprises I got when I opened a ticket was a ticket that explicitly banned AI. This was a surprise since I had a criteria of looking for non-AI-ban bug tickets. Upon further inspection, I realized that my tool was looking for a ban on the entire repo and was not looking at each individual bug ticket. This particular bug ticket had an AI ban for beginner-type issues.
Change: I added an additional check to look for an AI ban at the individual issue level, not just the repo level.
Result: The patch now says a scoped ban fails, so every future run has to reject it.

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

One key issue I discovered is that if you use `!`, it will bypass a lot of the tools. This isn't fixable by improving the hook — `!` is a different execution path that hooks can't see at all. The real fix is to make sure to run the voice guard in the terminal before posting, and avoid running in Claude with `!`. It is an inherent risk that cannot be overstated.

As I use these tools, they will keep evolving as I learn more nuances and hit interesting scenarios. Next, I would re-examine "hard to test" before falling back to a manual check, because rfoust's extraction showed that there was a way to automate the test. I would also add a human-filed check, because it would catch tickets written by a bot or AI.

**Tool limits.**

issue-scout: no way to check if a fix was already fixed upstream, no fork check, and newest-first ordering favors issues others grab first.
ship-gate: it only confirms every item on my list; however, it didn't tell me if the list was good enough. The example here was that a manual ASan run was listed as the regression test; however, later on a reviewer moved `loadWav()` and wrote a real test for it, `ax25_replay_wav_test`.
voice-guard: the `!` bypass described above — it can't see or guard commands run that way.
PR-monitor: only the MERGED path has actually been exercised. The feedback-in, branch-stale, and quiet/nudge forms are untested.
