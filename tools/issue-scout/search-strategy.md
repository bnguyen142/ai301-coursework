# Search strategy: where your scout looks, and why

<!--
THIS IS THE PART YOU WRITE. The scout in SKILL.md executes whatever
queries and filters you define here. It ships empty on purpose: the
strategy is your judgment about where YOUR first contribution lives,
and a scan without authored queries is just someone else's defaults.

A filled strategy must contain:

1. At least one query in the Queries section. Each query is a real
   `gh search issues` invocation, and each should trace to a sentence
   of your fit profile in scope.md: if you cannot say which sentence
   produced a query, the query is probably not yours. Search breadth
   is the point at this stage: prefer generous limits and let the
   ranking filters do the narrowing.

2. At least one ranking filter in the Ranking filters section. A
   filter is a condition or an ordering over fields the search
   results already carry (last activity date, assignee presence,
   label set, comment count, repo). It must be checkable from the
   result fields alone: if deciding it would need opening the issue
   page, it belongs in your rubric, not here. Ranking is the free
   stage; keep it free. Make at least one filter an ordering, or
   state your tie-break: with drop conditions alone, the scout takes
   the top of whatever order the searches happened to return, and
   the graded candidates stop being traceable to you.

3. The top-N line, kept or deliberately changed.
-->

## Queries

<!-- Real gh search invocations, one per line, each with a comment
naming the fit-profile sentence it came from. Example SHAPE (write
your own; these values belong to nobody):

gh search issues --label "good first issue" --language python --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url
-->
# From my profile: "Python is my strength, and I also know basic C and C++."
# and: "An issue whose expected behavior is stated plainly is worth more to me..."
gh search issues '"expected behavior"' --language python --label bug --state open --no-assignee --comments ">=1" --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url


# From my profile: "Python is my strength, and I also know basic C and C++."
# Narrowed with "good first issue": without it, C++ returned mostly emulators and Windows tools
gh search issues '"expected behavior"' --language "c++" --label bug --label "good first issue" --state open --no-assignee --comments ">=1" --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url


# From my profile: "Python is my strength, and I also know basic C and C++."
# Narrowed with "good first issue": without it, C returned mostly firmware
gh search issues '"expected behavior"' --language "c" --label bug --label "good first issue" --state open --no-assignee --comments ">=1" --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url

## Pacing

Run the queries one at a time, and wait 30 seconds after each one before
starting the next. Save each query's results to its own file as soon as it
returns. If a query fails with "secondary rate limit", keep the results
that came back and re-run only the failed queries. If the error carries a
`retry-after` header, wait that many seconds first. Otherwise wait 1
minute, then 10 minutes, then 1 hour between tries (GitHub's guidance: at
least a minute, longer each time). Stop after 3 failed retries and note it
in scan-notes.md. Never retry in a tight loop: GitHub says that can get
the account banned. Don't call `gh api rate_limit` to check; it does not
show this limit and still counts as a request. Do not split a query into
smaller ones; that means more requests, not fewer.

## Ranking filters

<!-- Conditions and orderings over the free result fields, applied in
the order written. State each so a stranger could apply it and get
your ordering. Example SHAPES (write your own):

- drop any result with an assignee
- drop any result not updated in the last 60 days
- prefer fewer comments over more
-->

1. Drop any result that has an assignee (`assignees` is not empty).
2. Drop any result with more than 3 comments (`commentsCount` > 3).
3. Order each query's survivors by most recent update first (`updatedAt`,
   newest first). An issue found by more than one query counts under the
   first query that found it (Q1, then Q2, then Q3).
4. Take the top 3 from Q1 (Python), then the top 1 from Q2 (C++) and the
   top 1 from Q3 (C), in that order. Python comes first because it is my
   strongest language ("Python first" in my profile). If a query has fewer
   survivors than its share, fill the empty slots with the next-newest
   leftovers from Q1, then Q2, then Q3.

## How many to grade

Grade the top **5** ranked results per scan.

<!-- Five is the default because grading is the only stage that costs
money: about $0.20 per candidate, so a scan is about $1, and you can
iterate the strategy several times for less than one full regression
run. You need one good issue, not ten. Raising this number is
allowed, but it is a decision you state here in a sentence, with the
cost you are accepting; it is not a number you bump because a scan
disappointed you once. -->
