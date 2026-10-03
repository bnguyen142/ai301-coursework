# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

bnguyen142

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5963081283

Plan for #72, built from my reproduction above (https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5861627631).

**Diagnosis.** `verify_password` passes the stored hash straight to `pwd_context.verify()` at `core/security.py:37`, with nothing around the call. My repro showed two routes out of it, not one:

- `'not_a_valid_bcrypt_hash'`, `''` and `'md5$abc$def'` raise `passlib.exc.UnknownHashError` (passlib cannot identify the scheme).
- `'$2b$12$abc'` raises a plain `ValueError: salt too small`. It has a valid bcrypt prefix, so it reaches the bcrypt handler and fails there.

A real bcrypt hash still returned `True` / `False` correctly, so the problem is how the function handles a stored hash it cannot use.

**What I plan to change** (two files):

- `core/security.py`: wrap the verify call in `try` / `except ValueError: return False`. passlib's `UnknownHashError` is a subclass of `ValueError` (checked on passlib 1.7.4), so one clause covers both routes. I'll add a comment saying so, and update the docstring's `Returns:` line.
- `tests/unit/test_security.py`: delete the `xfail(strict=True)` marker on `test_verify_with_wrong_hash_format`, as the issue asks, and add one test for the `'$2b$12$abc'` case, which the existing test doesn't cover.

**Not changing:** `hash_password`, the token functions, or the login route (reading `api/routes/auth.py:80`, it already rejects the login when `verify_password` returns `False`). I'm also leaving out the passlib/bcrypt `__about__` warning from my repro, which is unrelated.

**How I'll check it.** I'll re-run my repro steps against the change:

- `pytest --runxfail tests/unit/test_security.py -k wrong_hash_format -v` should go from `FAILED` to `1 passed`;
- the same test without `--runxfail` should pass with no `XPASS(strict)`;
- all four stored values above should return `False`, with the controls unchanged;
- `make lint`, `make typecheck` and `make test-unit` should pass.

**Open question:** catching `ValueError` is wider than the four inputs I tested. Any `ValueError` from passlib during verification would become a failed login instead of an error. I read that as the fail-closed behavior the issue asks for, but if the intent was to catch only `UnknownHashError`, I'd like to know before I open the PR. I'm also on Python 3.14.7 locally, not CI's 3.11, so CI will be the first 3.11 run.

*Disclosure: I'm working with AI assistance. The reproduction, the plan and the verification are mine.*

---

## Your branch

**Branch**

`fix/72-verify-password-fail-closed`

https://github.com/bnguyen142/pathreview-ai301-fa26-s3/tree/fix/72-verify-password-fail-closed (commit `626ebad`)

**Evidence**

Same commands before and after. `repro72.py` is my Unit 2 reproduction script, extended to list the four malformed stored hashes literally.

**Before** (unchanged `main`, `2f4e82f`):

```
# BEFORE — branch fix/72-verify-password-fail-closed at 2f4e82f (unchanged main), Python 3.14.7, passlib 1.7.4, bcrypt 4.3.0

$ .venv/bin/python -m pytest --runxfail tests/unit/test_security.py -k wrong_hash_format -v --tb=line --no-header -p no:warnings
=================================== FAILURES ===================================
E   passlib.exc.UnknownHashError: hash could not be identified
/home/bhn/Projects/Codepath/pathreview-ai301-fa26-s3/.venv/lib/python3.14/site-packages/passlib/context.py:1132: passlib.exc.UnknownHashError: hash could not be identified
=========================== short test summary info ============================
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
======================= 1 failed, 24 deselected in 0.37s =======================

$ .venv/bin/python -m pytest tests/unit/test_security.py -k wrong_hash_format -v --no-header -p no:warnings
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format XFAIL [100%]

====================== 24 deselected, 1 xfailed in 0.36s =======================

$ .venv/bin/python repro72.py 2>/dev/null
control, valid hash, right password: True
control, valid hash, wrong password: False
verify_password('password', 'not_a_valid_bcrypt_hash') -> raised passlib.exc.UnknownHashError: hash could not be identified
verify_password('password', '$2b$12$abc') -> raised builtins.ValueError: salt too small (bcrypt requires exactly 22 chars)
verify_password('password', '') -> raised passlib.exc.UnknownHashError: hash could not be identified
verify_password('password', 'md5$abc$def') -> raised passlib.exc.UnknownHashError: hash could not be identified

$ .venv/bin/python -m pytest tests/unit/test_security.py -q -p no:warnings
.....................x...                                                [100%]
24 passed, 1 xfailed in 7.62s

$ make lint
.venv/bin/ruff check .
All checks passed!
exit: 0

$ make typecheck
.venv/bin/mypy api/ core/ ingestion/ rag/ agent/ safety/
Success: no issues found in 76 source files
exit: 0

$ make test-unit   (unchanged main, for comparison)
================= 375 passed, 53 xfailed, 2 warnings in 8.80s ==================
```

**After** (branch `fix/72-verify-password-fail-closed`):

```
# AFTER — branch fix/72-verify-password-fail-closed, working tree on 2f4e82f + the change, Python 3.14.7, passlib 1.7.4, bcrypt 4.3.0

$ .venv/bin/python -m pytest --runxfail tests/unit/test_security.py -k wrong_hash_format -v --tb=line --no-header -p no:warnings
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED [100%]

======================= 1 passed, 25 deselected in 0.31s =======================

$ .venv/bin/python -m pytest tests/unit/test_security.py -k wrong_hash_format -v --no-header -p no:warnings
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED [100%]

======================= 1 passed, 25 deselected in 0.29s =======================

$ .venv/bin/python -m pytest tests/unit/test_security.py -k malformed_bcrypt -v --no-header -p no:warnings
tests/unit/test_security.py::TestSecurity::test_verify_with_malformed_bcrypt_hash PASSED [100%]

======================= 1 passed, 25 deselected in 0.20s =======================

$ .venv/bin/python repro72.py 2>/dev/null
control, valid hash, right password: True
control, valid hash, wrong password: False
verify_password('password', 'not_a_valid_bcrypt_hash') -> returned False
verify_password('password', '$2b$12$abc') -> returned False
verify_password('password', '') -> returned False
verify_password('password', 'md5$abc$def') -> returned False

$ .venv/bin/python -m pytest tests/unit/test_security.py -q -p no:warnings
..........................                                               [100%]
26 passed in 7.58s

$ make lint
.venv/bin/ruff check .
All checks passed!
exit: 0

$ make typecheck
.venv/bin/mypy api/ core/ ingestion/ rag/ agent/ safety/
Success: no issues found in 76 source files
exit: 0

$ make test-unit

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
================= 377 passed, 52 xfailed, 2 warnings in 11.15s =================
exit: 0

$ .venv/bin/black --check core/security.py tests/unit/test_security.py
All done! ✨ 🍰 ✨
2 files would be left unchanged.
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Partial run, `--only calib-01,calib-03,pkg-06,pkg-15` (with `--include-calibration`): 2/2 scored items; both calibration packages also agreed.
2. Full run: 18/20 (bar: 18/20: PASS). Disagreed on pkg-14 and pkg-20.
3. Partial run, `--only pkg-20,pkg-04,pkg-09,pkg-03,pkg-05`: 5/5 scored items.
4. Confirming full run (saved at the time, then replaced by run 5): 19/20 (bar: 18/20: PASS).
5. Full run after renaming "student" to "author" in the evidence guide's live pointers (`--save-run eval-run.txt`): 19/20 (bar: 18/20: PASS).

**Package analysis**

**pkg-14** (zellij #5174, OSC color responses leaking into the pane on SSH reattach). Category: clear-accept. **Gold label: accept. My rubric's verdict: reject**, in all three full runs (2, 4 and 5).

My rubric failed it on `narrow-scope` and `buildable` (plus `evidence-supports-cause` and the preferred `honest-unknowns`). The deciding text is the plan's Files line: "the client attach/reattach path in `zellij-server` (session connection handling) and `zellij-client`'s terminal query issuance; exact functions to be pinned in the PR after tracing the query issuance with debug logs." Both checks ask the plan to name the actual file and function it will change. This plan names two crates and a code path, and leaves the exact location to be found later. `evidence-supports-cause` read the cache-clear control step as contradicting the plan's cause; I think that reading is debatable, because the control is also consistent with the plan.

I read pkg-14 as a more complex bug than most of the set. The fix spans the server and the client. The plan says the exact sites still need investigating with debug logs. And it borders on things outside the ticket's own evidence: the Windows session-switch variant from the thread, and how theme detection caches results. The plan leaves both out, and says so. The lecture explains why that makes it ready: two of the accepts are "scoped-down on purpose: deferring part of the issue, and saying so, is ready." pkg-14 is that kind of plan. My rubric can't tell honest deferral apart from a plan that is just vague, so it holds both.

I didn't change the rubric for this package. I would rather the check flag a plan like this and let a person read it, look at what the plan points to, and decide, than loosen it so vague plans slip through. `buildable` is the check aimed at vague plans, and all 3 unbuildable packages failed it. There will always be edge cases like this one. If they start coming up often, I can narrow the check later, for example by accepting a named code path when the plan says how it will find the exact site. I treat the rubric as something that changes with experience, not a finished product.

**Check rationale**

From `tools/plan-check/rubric.md`, check `maintainer-alignment` (required), pass condition as it reads now:

> Pass if the plan follows any direction a maintainer has given in the thread. A plan that disagrees with a maintainer still passes if it says so openly and states it will not build the disputed change until the maintainer agrees. Fail if the plan goes ahead with an approach a maintainer ruled out or argued against, without raising the disagreement. Also fail if the plan comment breaks a rule the repo states for contributions. AI disclosure: if the repo's policy asks for disclosure of AI use in comments, issues, or "any form", the plan comment must include one, and a comment without it fails. Do not require proof that AI was used, because every plan in this setting is written with AI help. A disclosure ask that covers only pull requests or code does not apply to a plan comment.

**Why the disagreement part reads this way.** I didn't want "disagrees with a maintainer" to be an automatic fail. A contributor's evidence can be right, and maintainers can be wrong. But a maintainer often knows things the thread doesn't show: where that code is headed, approaches that were tried and dropped, or constraints that were never written down. pkg-20 has an example: the maintainer rejected recomputing `prev` at every use because it's a hot path, and nothing in the bug report says that code is performance-critical. So the line I drew is that a plan may disagree openly, but it must not build the disputed change until the maintainer agrees. Building against stated direction wastes the maintainer's review time and usually gets the PR closed.

**Why the disclosure part reads this way.** It's the part I revised. The first version just said "breaks a rule the repo states for contributions, such as a required AI disclosure." In run 2, pkg-20 got through: ghostty's policy says "All AI usage in any form must be disclosed," the comment had no disclosure, and the grader excused it because there was "no evidence in the bundle that AI was used." So I added "do not require proof that AI was used." I rejected the simpler fix, "any comment without a disclosure fails," because pkg-09's repo asks for disclosure only in the pull request and states "no disclosure ask for issue comments," and that plan is correct without one. So the condition depends on what the policy actually covers. I re-ran pkg-20 with pkg-04, pkg-09, pkg-03 and pkg-05 as canaries (5/5). pkg-20 then failed on this check, quoting the "any form" policy, and the confirming run held at 19/20 with thread-convention 2/2.

**Trade-offs**

For `maintainer-alignment`, the check quoted above:

- **It fails a missing disclosure without proof that AI was used.** In a repo whose policy asks for disclosure in comments or "any form", a comment with no disclosure fails even if the author truly wrote it alone. I accept that false reject. In this setting every plan is written with AI help, adding a one-line disclosure costs the author almost nothing, and leaving it out in a strict repo like ghostty can get the contribution closed.
- **It depends on reading what a policy covers.** Clear policies are easy: ghostty's "any form", or pkg-09's "no disclosure ask for issue comments". A vague policy leaves the grader guessing, and under my verdict rule an unclear grade counts as a fail.
- **The disagreement clause can't see how active a maintainer is.** A plan that disagrees and says it will wait passes, even in a thread where no maintainer will answer, and that plan could stall. I left maintainer activity out on purpose, because a grader can't measure it from a package.

How I know the revision didn't cost anything elsewhere: after tightening the disclosure clause, I re-ran it with `--only` on pkg-20 plus canaries pkg-04, pkg-09, pkg-03 and pkg-05. The canaries are the other thread-convention package and three clear-accepts whose repos have AI policies. All 5 agreed, and the next two full runs held at 19/20 with no new disagreements.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
