# Scope: where to look, and who is looking

<!--
This file is the skill's field of view. The rubric (rubric.md) decides
whether an issue is GOOD; the scope decides which issues are candidates
at all, and whose hands the issue would land in. It applies in live mode
only: in eval mode the bundle is the whole world and this file is
ignored.

Two parts. Staff wrote the first; you write the second.
-->

## Where candidates come from

Only issues in the course's Path Review repository are candidates:

- Repo: `codepath/pathreview-ai301-fa26-s3`

Do not search, fetch, or grade issues from any other repository, however
promising. The wider GitHub comes later in the course; for now the field
is Path Review.

**Path Review house rule.** Path Review is a classroom, and your
classmates are not strangers. Ignore the usual claim signals here: other
students' claim comments (and there may be several on one issue) do not
block an issue, and finding some on the issue you want is normal. Claim
anyway: course credit attaches to the pull request you open, not to
whether it merges, so a shared issue costs nobody anything. Everything
else in the rubric applies as written.

## Your fit profile

<!-- YOU write this part: a few sentences about you. What languages and
tools you have actually used, what you want to get better at, anything
you want to avoid. The skill uses this only to RANK the issues your
rubric accepts, never to change a verdict: fit cannot rescue an issue
your rubric rejects, and cannot sink one it accepts. -->

Python is the language I work in, along with a lot of SQL — my day job is
backend and data plumbing for a retail kiosk system, so I am used to tracing
a value through a pipeline and working out why it is not turning up where it
should.

Tests are my strongest ground. Writing pytest tests, reading a failing one,
and working backwards from the failure to the cause is the part of this I am
genuinely good at. An issue that already has a test — failing, xfail-marked,
or obviously missing — plays to that, and an issue whose expected behavior is
stated plainly is worth more to me than a tidy writeup with no way to tell
when I am finished.

I also already know this codebase: I spent AI201 in PathReview, so the
FastAPI routes, `core/services/`, the ingestion parsers and the pytest suite
are familiar and I do not need a day to work out where things live.

What I want to get better at is landing a change in code I did not write
without needing the whole design explained to me first.

What I want to avoid is a change that reaches across several modules at once
and turns on a fundamental architectural decision. A bug I can corner in one
or two files is work I can finish; a change that requires settling how the
system should be structured before I can write any of it is not something I
can carry as a first contribution, however well the issue is written.
