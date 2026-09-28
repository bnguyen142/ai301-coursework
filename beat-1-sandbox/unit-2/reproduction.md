# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

---

## Your identity upstream

**GitHub username**

bnguyen142

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5861293529

I'd like to claim this one.

What I'm going to look at: the issue reports that `verify_password` in
`core/security.py` lets passlib's `UnknownHashError` escape instead of
returning `False` for a hash it can't identify. Reading
`tests/unit/test_security.py`, the covering test
`test_verify_with_wrong_hash_format` is marked
`@pytest.mark.xfail(strict=True)` against manifest id H-05.

My first step is reproduction, not a fix. I'll set the project up from the
repo's docs and run that test so the exception actually surfaces — under
`xfail` the suite swallows it and reports green — then check that what I get
is the `UnknownHashError` the issue names rather than an adjacent failure.
I'll post a reproduction report with the environment I used, the exact
command, and the output.

*Disclosure: I'm working with AI assistance. The reproduction and the
verification are mine.*

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5861627631

Reproduction report for #72. **Result: reproduced** on current `main`.

### Environment

- OS: Omarchy Linux, kernel 7.2.5-3-omarchy, x86_64
- Python 3.14.7
- passlib 1.7.4, bcrypt 4.3.0, pytest 9.1.1
- Repo at commit `2f4e82f` (2026-09-16), my fork of
  `codepath/pathreview-ai301-fa26-s3`
- Installed with `python3 -m venv .venv` then `pip install -e ".[dev]"`. No
  database or Redis is involved: the covering test is a unit test, so I did
  not need `docker compose up` or `alembic upgrade head` from the setup docs.

One difference worth stating up front: CI and the mypy config target Python
3.11, and I ran 3.14.7. `requires-python = ">=3.11"` allows it, but it is not
the version the project builds against, so that is a gap between my run and
the project's own baseline.

### Steps

```bash
git clone https://github.com/codepath/pathreview-ai301-fa26-s3.git
cd pathreview-ai301-fa26-s3
git checkout 2f4e82f
python3 -m venv .venv
.venv/bin/pip install -e ".[dev]"
```

(I worked in my own fork, but `2f4e82f` is also upstream `main` as of this
run, so cloning upstream directly gives the same tree and anyone can check it.)

### What the default test run shows — which is nothing

```
$ .venv/bin/python -m pytest tests/unit/test_security.py -k wrong_hash_format -v
================= 24 deselected, 1 xfailed, 1 warning in 0.83s =================
```

The covering test is marked `@pytest.mark.xfail(strict=True)`, so the suite
swallows the failure and reports green. Running the test as-is does not
demonstrate the bug.

### The failure, with the marker defeated

```
$ .venv/bin/python -m pytest --runxfail tests/unit/test_security.py \
      -k wrong_hash_format -v --tb=short --no-header -p no:warnings

tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format FAILED [100%]

=================================== FAILURES ===================================
_______________ TestSecurity.test_verify_with_wrong_hash_format ________________
tests/unit/test_security.py:227: in test_verify_with_wrong_hash_format
    result = verify_password("password", wrong_hash)
core/security.py:37: in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
.venv/lib/python3.14/site-packages/passlib/context.py:2343: in verify
    record = self._get_or_identify_record(hash, scheme, category)
.venv/lib/python3.14/site-packages/passlib/context.py:2031: in _get_or_identify_record
    return self._identify_record(hash, category)
.venv/lib/python3.14/site-packages/passlib/context.py:1132: in identify_record
    raise exc.UnknownHashError("hash could not be identified")
E   passlib.exc.UnknownHashError: hash could not be identified
=========================== short test summary info ============================
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
======================= 1 failed, 24 deselected in 0.35s =======================
```

The call chain is the point: the exception is raised inside passlib and
escapes through `core/security.py:37`, which is the line the issue names. The
caret markers pytest prints under each frame are removed above for width;
nothing else is trimmed.

### Minimal trigger, with a control

Outside pytest entirely. The script I ran, in full:

```python
from passlib.context import CryptContext
from core.security import verify_password

valid = CryptContext(schemes=["bcrypt"]).hash("password")
print("control, valid hash, right password:", verify_password("password", valid))
print("control, valid hash, wrong password:", verify_password("wrong", valid))
try:
    print("malformed hash:", verify_password("password", "not_a_valid_bcrypt_hash"))
except Exception as e:
    print(f"malformed hash: raised {type(e).__module__}.{type(e).__name__}: {e}")
```

Its output, in full:

```
$ .venv/bin/python repro72.py
(trapped) error reading bcrypt version
Traceback (most recent call last):
  File ".venv/lib/python3.14/site-packages/passlib/handlers/bcrypt.py", line 620, in _load_backend_mixin
    version = _bcrypt.__about__.__version__
              ^^^^^^^^^^^^^^^^^
AttributeError: module 'bcrypt' has no attribute '__about__'
control, valid hash, right password: True
control, valid hash, wrong password: False
malformed hash: raised passlib.exc.UnknownHashError: hash could not be identified
```

The two control calls return `True` and `False` correctly, so passlib and
bcrypt are working in this environment and the verification path is healthy
for hashes passlib recognises. The malformed hash is the only one of the three
that raises, which puts the failure in `verify_password` in `core/security.py`
rather than in my setup, my branch, or my input.

### A second exception type, which the issue does not mention

Since one malformed input is a thin basis for saying what the function does, I
tried a few more, same environment, same script shape:

```
'not_a_valid_bcrypt_hash'    -> raised UnknownHashError: hash could not be identified
'$2b$12$abc'                 -> raised ValueError: salt too small (bcrypt requires exactly 22 chars)
''                           -> raised UnknownHashError: hash could not be identified
'md5$abc$def'                -> raised UnknownHashError: hash could not be identified
```

A hash that *looks* like bcrypt but has a malformed salt raises `ValueError`
from the bcrypt handler rather than `UnknownHashError` from the resolver, so
they escape by different routes. I am recording this because a fix that
catches only `UnknownHashError` would still let the `ValueError` case through,
and both are the same failure from a caller's point of view: verification
against a stored hash it cannot use should fail closed rather than raise.

I have not tried to enumerate every malformed form — these are the four I
ran.

### Expected vs actual

- Expected: `verify_password` returns `False` for a hash it cannot identify —
  fail closed.
- Actual: the exception propagates out of the function —
  `passlib.exc.UnknownHashError` for a hash passlib cannot identify, and
  `ValueError` for one that looks like bcrypt but carries a malformed salt.

### One thing worth noting

The `(trapped) error reading bcrypt version` block at the top of that output
is separate from this issue. passlib catches it internally and carries on —
the control calls above still hash and verify correctly, so it does not
affect this reproduction. My reading of it, which I have not verified against
passlib's changelog, is that passlib 1.7.4 is looking for a `__about__`
attribute that newer bcrypt releases no longer expose.

It looks like its own issue against the dependency pins rather than something
to fold in here. I haven't filed anything — flagging it in case it is already
known or intentional.

## Eval iterations

**Run history**

Four runs, in order:

1. `3/3` — smoke run, `--limit 3`, to confirm the harness was reading both my
   rubric and my evidence guide before spending on a full run.
2. `16/20` — first full run. Below the bar, and the category floor was unmet:
   `disclosure 0/1`. Four disagreements: pkg-09 and pkg-10 rejected against a
   gold label of accept, pkg-19 and pkg-20 accepted against a gold label of
   reject.
3. `10/10` — partial `--only` run over those four plus six canaries
   (pkg-01, pkg-02, pkg-03, pkg-04, pkg-05, pkg-12), after four revisions.
4. `19/20` — confirming full run. `bar: 18/20: PASS`, every category matched:
   `clear-accept 7/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3
   wrong-target 4/4`. This is the run committed as `eval-run.txt`.

Runs 1 and 3 were partial and could not have written the run file; runs 2 and
4 were complete.

**Package analysis**

`pkg-20` (ghostty). My rubric returned **accept**; the gold label is
**reject**; on my first full run they disagreed, and this single package was
the whole category floor — the run reported `disclosure 0/1`.

My `ai-policy-respected` check graded it pass, and the reason it gave was:

> Neither comment references AI use; policy requires disclosure only when AI is
> actually used, and nothing indicates it was.

That reasoning follows exactly from how I had written the check. I had said the
comments must comply with what the policy "explicitly states", and where
disclosure is required "at least one of the comments states that AI was used" —
but I never said what to assume about a package that says nothing. Read
literally, silence is not evidence of AI use, so the requirement never fired.

The gold label treats course packages as AI-assisted work by construction, so
silence is not neutral: it *is* the missing disclosure. ghostty's policy is
explicit that "All AI usage in any form must be disclosed" and that
"AI-assisted issues and comments must be reviewed and edited by a human", which
reaches issue comments and not only pull requests.

The fix had a trap in it. `pkg-09` also carries a disclosure requirement —
"must state the tool and the extent of its use in the pull request" — and is a
gold **accept**. Tightening the check to "a disclosure requirement exists, so
the comments must disclose" would have fixed pkg-20 and broken pkg-09 for a net
gain of nothing. What separates them is scope: pkg-09's policy says outright
that it makes "no disclosure ask for issue comments". So the check now states
the AI-assisted assumption *and* requires the policy to reach issue comments
before it bites.

**Check rationale**

The check as it now reads in `tools/repro-check/rubric.md`:

| behavior-matches-issue | The artifacts in the repro report — output excerpts, logs, tracebacks — read against the error or behavior the issue describes. | Either the artifact shows the specific error or behavior the issue names — the same exception, message, or observable result — or the report states plainly that it could not reproduce the issue and shows the steps it ran and the output it obtained instead. An honest negative result is a faithful report of what happened and passes. What fails is a mismatch presented as a match: a different failure offered as the issue's, a test that merely passed or was skipped offered as proof, or a claim with no artifact attached. | required |

The first half is what I started with: the artifact has to show the error the
issue names. That is the check that holds `calib-03` in the activity, where a
report reproduced a `Missing key/value separator` parse error and presented it
as the issue's `panic: not a string`.

The second half is what my first full run forced on me. `pkg-09` and `pkg-10`
are honest cannot-reproduce packages — a real attempt, artifacts shown, and a
plain statement of what differed — and both are gold accepts. My original
wording demanded the artifact show the issue's error, so it failed both for not
showing an error that did not occur. The grader's own words on pkg-10 were
"No failure occurred to isolate".

What makes that worth recording is where the principle already lived. My
`references/evidence-guide.md` said, before that run:

> A report that says it could not reproduce is honest and complete when it shows
> the steps it ran and the output it got instead; that outcome is a pass, not a
> failure.

The evidence guide had it right and the rubric did not. The rubric is what
grades, so the principle had no effect until I wrote it into the check itself.
I made the same mistake a second time with `environment-and-deps-named`, which
is what pkg-11 still disagrees on.

**Trade-offs**

Two things this cost, both measured rather than guessed.

First, the canaries. Two of my four revisions loosened a check (the
cannot-reproduce carve-out in `behavior-matches-issue` and
`cause-isolated-from-the-reporter`) and two tightened one (`ai-policy-respected`
and promoting `claim-promises-only-investigation` from preferred to required).
Loosening can flip a package that already agreed, so I re-ran six canaries
alongside the four fixes: pkg-02 and pkg-04 for the loosened checks, and
pkg-01, pkg-03, pkg-05 and pkg-12 — every clear-accept whose repo carries an AI
policy — for the tightened one. All six held, and the confirming full run
agreed with them.

Second, a case I accept it will miss. `pkg-11` is a gold accept that my
`environment-and-deps-named` check rejects, because the report names yq, the OS
and the install method but not the vendored go-yaml version, and my check says
"naming the project but not the dependency versions the bug turns on fails".
For a tool installed as a Homebrew binary that version is not reasonably
obtainable, and yq's own bug-report template asks only for "the yq version,
operating system, how yq was installed" — which the report supplies. So the
check is stricter than the project itself.

Worth noting alongside that: pkg-11 **agreed** on my first full run and
disagreed on the confirming one, on a check I never edited between them. The
harness grades with Sonnet, so a genuinely borderline package can land either
way. Part of the gap between 19 and 20 is grader variance rather than rubric
quality, and the fix for a flapping check is wording that forecloses the second
reading — not a different threshold.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
