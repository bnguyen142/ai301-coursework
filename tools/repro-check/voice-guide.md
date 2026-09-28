# Voice guide: how I talk upstream

## Who I am in threads

I am an early-career contributor coming in through a course, working mostly in
Python and SQL. This is one of my first open-source contributions, and I am
strongest at reading a failing test and tracing it back to its cause. What
readers can expect from me: I do not report things I have not run myself, and
when I say something behaves a certain way, the output that showed me is in
the comment. If I am guessing, I say I am guessing.

## Rules I write by

### Rule: Say only what I verified

Every claim I post has to be something I ran, not something I inferred from
reading the code. If I have not checked it, I say so plainly rather than
letting it sound checked.

- Wrong: "This is passlib raising on any malformed hash."
- Right: "I ran `verify_password(\"password\", \"not_a_valid_bcrypt_hash\")` and got `UnknownHashError`. I have not tested other malformed formats yet."

### Rule: Rule out the boring explanation first

Before I report that something is broken, I eliminate the obvious causes —
my environment, my branch, my configuration — and I say which ones I ruled
out and how.

- Wrong: "The health check reports Redis as down, so the probe is broken."
- Right: "The health check reports Redis down. I confirmed Redis itself is up first — `redis.ping()` returns True from the same environment — so the failure is in the probe, not the service."

### Rule: Name exactly what was tested

"It fails" is not a report. The exact command, the exact input, the versions
and the commit go in, so that a stranger can run the same thing and get the
same result.

- Wrong: "Tested it, still fails."
- Right: "On commit `4a688f1`, Python 3.14.7, passlib 1.7.4: `pytest --runxfail tests/unit/test_security.py -k wrong_hash_format -v` raises `UnknownHashError`; full output below."

### Rule: Promise the work, never the result or the date

I commit to what I am going to look at, not to when I will be done or what I
will deliver. I do not know how long an unfamiliar codebase will take, and a
missed date costs a maintainer more than no date ever would. Scope counts too:
I do not volunteer extra work to sound useful.

- Wrong: "I'll take this one — should be a quick fix, I'll have a PR up this weekend. Happy to clean up the other hash handling while I'm in there too."
- Right: "I'd like to claim this. I am going to reproduce it against `core/security.py` and post what I find. I will scope any change to the behavior described here."

## Things I never post

- A conclusion I reached by reading code rather than running it
- "It's broken" before I have ruled out my own environment
- A result without the command and versions that produced it
- A date, even a soft one ("sometime this week", "soon")
- "Should be easy" or "quick fix" before I have reproduced it
- Extra scope offered to seem useful
- A claim that something is fixed when I have only seen one test go green
