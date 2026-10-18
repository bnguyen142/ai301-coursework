# issue-scout scan notes

Dated friction log: what fought me, and what I changed because of it.
Evidence files: ~/Projects/beat2-scout/site-check-2026-10-18.md,
~/Projects/beat2-scout/beets-2491-check-2026-10-18.md

## 2026-10-18

- Loosened my requirement of Python only, because there were too few good
  candidates. Evidence: of 184 Python bugs on the Issue Selection site, only 6
  were free after checking, and none fit. Added C and C++ queries (my profile
  says "I also know basic C and C++").
- The site's "Python" filter matches any repo with some Python in it; 154 of
  257 Python + bug issues were in repos that are mainly C++, Fortran, etc.
  `gh --language` only matches the repo's main language.
- Had to check the comments, not just the search fields. "Open, no assignee,
  no linked PR" still let through issues that were claimed in a comment, had
  an open PR that only mentions the issue (tm_devices #440, Red-DiscordBot
  #6794), or were already fixed (docling #4609, beets #5070), or whose code
  was being deleted (openedx #37995).
- Had to check the actual code to see if we could replicate the issue, and
  that the issue made sense to fix instead of having to redesign something.
  Example: beets #2491. A previous patch (commit 69faa58, 2024-05) changed the
  behavior, so singles are no longer checked against album tracks. I want to
  confirm with the maintainer whether that was intended.
- First bare query (`--language python --label bug`) returned only the last
  25 minutes of issues, mostly bulk bot-filed issues from tiny repos.
  Changed: added the phrase "expected behavior" (English, and it matches my
  profile) and `--comments ">=1"` (a human looked at it).
- C and C++ queries returned mostly emulators, game mods, and device
  firmware. Changed: added `--label "good first issue"` to both.
- Ranking: drop assigned, drop more than 3 comments, newest update first.
- Hit the GitHub API rate limit, which is expected after a day of heavy
  searching (secondary rate limit on search). A scan can fail at the search
  step even when the strategy is fine; wait and retry.
- The rate limit lasted longer than a few minutes: a dry run of my 3 queries
  was refused again later the same day (Python query went through, C++ query
  got "HTTP 403: You have exceeded a secondary rate limit"). Lesson: plan
  scans away from big manual search sessions, and retry once after about an
  hour instead of retrying in a loop.
- `ai-policy-permits` only covers repo-wide bans. Beets has an AI ban
  scoped to one kind of issue: `AI_POLICY.md` says "AI is not for 'good
  first contribution' issues." Beets #2491 has the `good first issue` label,
  so I graded it a fail. The check as written doesn't say how to grade a ban
  that covers only some issues. Possible patch: a ban on AI for the issue's
  own label or type counts as a refusal.
- `unclaimed` doesn't treat a label that hands the issue to a bot as a claim.
  On AetherSDR #4907 the owner added `aetherclaude-eligible` on 2026-09-29,
  which authorizes the project's own AI agent to fix it. There's no assignee,
  no PR and no claim comment, so it passed. But a human PR could race the
  bot. Possible patch: count a maintainer applying an agent-dispatch label in
  the last 180 days as a claim, unless a maintainer invites human takers.
- Focused on 3 issues: beets #2491, Cataclysm-BN #9875, AetherSDR #4907.
  My first candidate, beets #2491, has an AI ban (`AI_POLICY.md`: "AI is not
  for 'good first contribution' issues", and #2491 is labeled
  `good first issue`), so this rules out the ticket. That leaves two.
- Full scan hit the secondary rate limit again: Q1 (Python) returned 100
  results, then Q2 (C++) and Q3 (C) got "HTTP 403: You have exceeded a
  secondary rate limit". The main search limit showed 0 of 30 used, so it
  was the burst limit, not the query count. Q1 probably worked because it
  ran first, so putting C++ and C first would not help. Break up the
  requests instead: wait 20-60 seconds between queries, don't scan right
  after other GitHub searching, and save each query's results as it
  finishes so a retry only re-runs the ones that failed. This probably hits
  most students too, since the scan runs every query back to back.
- Changed: added a Pacing section to search-strategy.md. Queries run one
  at a time with 30 seconds between them, and each query's results are
  saved as it returns. On "secondary rate limit", only the failed queries
  are re-run: wait for `retry-after` if the error gives it, otherwise
  1 minute, then 10 minutes, then 1 hour, and stop after 3 failed
  retries. No `gh api rate_limit` checks (they don't show this limit and
  still count as a request). Source: GitHub Docs, "Rate limits for the
  REST API": no published burst threshold; wait at least a minute and
  longer each time. My 2026-10-10 scripts used `sleep 2.2` between
  searches and were never blocked, so 30 seconds is cautious.
- Full scan finished (Q2 and Q3 re-run 30 seconds apart, no block): 231
  results, 160 after filters, and all top 5 came from Q1 (Python). Python
  first is what I want, because it is my strongest language ("Python
  first" in my profile). But it got there by luck, not by my rule: Python
  issues are updated far more often, so "newest update first" favored
  them. On a slow Python day C/C++ would fill the top 5. The best C/C++
  result (rizin #6908) landed 7th and was never graded. Possible patch:
  make the order explicit, for example top 3 from Q1 and top 1 each from
  Q2 and Q3, so Python first traces to my profile.
- The rubric accepted santosh-sankranthi/falkordb-py #6, an issue in a
  personal fork created the same day (2026-10-18, 0 stars, all 6 issues
  opened that day, owner replies read like a bot). `commits-recent` passed
  on commits inherited from upstream FalkorDB/falkordb-py. Nothing checks
  that the repo is the real project and not a copy. Possible patch: a
  required check that fails forks, or a strategy filter dropping forks
  (`gh search issues` does not return a fork flag, so it would have to go
  in the rubric).
- Changed: replaced ranking filter 3 in search-strategy.md. Each query's
  results are now ordered newest update first on their own, and the top 5
  is taken as 3 from Q1 (Python), then 1 from Q2 (C++) and 1 from Q3 (C).
  An issue found by more than one query counts under the first query that
  found it; if a query comes up short, its slots fill from the leftovers,
  Q1 first. This makes "Python first" a written rule instead of luck. On
  today's results it would swap the two Calibre-Web-NextGen issues for
  memgraph #471 and rizin #6908 (both `good first issue`, not yet graded).
- Second repo with an AI ban on good first issues: rizin #6908 (top C
  result under the new ranking rule). CONTRIBUTING.md says "Using AI tools
  to fix issues labelled as 'good first issues' is forbidden". Same shape
  as beets earlier today, so the `ai-policy-permits` gap (only repo-wide
  bans covered) has now come up twice. It was also claimed the same
  morning (Master-Zero1, 2026-10-18, unanswered). The ranking ordered it
  by update date and couldn't see either problem; only the rubric caught
  them.
- Changed: patched `ai-policy-permits` in rubric.md: "A scoped AI ban on
  the issue's label counts as a refusal." The repos I was looking at did
  not ban AI on the entire repo; the ban was specific to good first
  issues. Beets had it in AI_POLICY.md and rizin in CONTRIBUTING.md. The
  ranking can't see AI policies, so both reached grading, and grading
  failed them, but the check as written only covered repo-wide bans, so a
  stricter reading could have passed them.
  Also added to the rubric: the ban's wording does not have to match the
  label. Beets' label was `good first issue` but its ban is on "good
  first contribution" issues.
- Regression after the AI-policy patch: Unit 1 eval harness, full run,
  20/20 (bar 18/20: PASS), no flips. Saved as eval-run-2026-10-18.txt in
  the skill folder. Only 1 of the 20 cases is a policy case, and none has
  a ban scoped to a label, so this shows the patch broke nothing, not that
  it works. Beets #2491 and rizin #6908 are the evidence that it works.
