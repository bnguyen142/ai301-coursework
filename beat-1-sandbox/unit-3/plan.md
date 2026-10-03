# Plan: #72 — `verify_password` fails closed on unusable stored hashes

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72
Built from my reproduction: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5861627631
Base: `main` at `2f4e82f`, my fork `bnguyen142/pathreview-ai301-fa26-s3`

## Diagnosis

`verify_password` in `core/security.py` hands the stored hash straight to
`pwd_context.verify()` on line 37, with nothing around the call. When passlib
cannot use the stored value, the exception propagates out of the function
instead of the function returning `False`.

My reproduction shows two routes out, not one:

| Stored value | What escaped |
|---|---|
| `'not_a_valid_bcrypt_hash'` | `passlib.exc.UnknownHashError: hash could not be identified` |
| `''` | `passlib.exc.UnknownHashError` |
| `'md5$abc$def'` | `passlib.exc.UnknownHashError` |
| `'$2b$12$abc'` | `ValueError: salt too small (bcrypt requires exactly 22 chars)` |

The first three fail in passlib's resolver, which cannot identify a scheme.
The fourth has a valid bcrypt prefix, so passlib routes it to the bcrypt
handler, which then rejects the 3-character salt. The controls in the same
run behaved correctly: a real bcrypt hash returned `True` for the right
password and `False` for a wrong one. So the fault is in how
`verify_password` handles an unusable stored hash, not in verification
generally.

The issue names only `UnknownHashError`, but its stated intent is broader:
"verification against a malformed hash should fail closed (return `False`),
not raise." The `'$2b$12$abc'` case is the same failure as the others from a
caller's point of view, so this plan covers both routes.

## Scope

**In:**
- `verify_password` returns `False` when passlib cannot use the stored hash:
  both the `UnknownHashError` inputs and the `ValueError` input above.
- Delete the `@pytest.mark.xfail(strict=True, ...)` marker on
  `test_verify_with_wrong_hash_format`, as the issue and
  `docs/CONTRIBUTING.md` require.
- Add one test covering the `'$2b$12$abc'` route. The existing test covers
  only the `UnknownHashError` route.

**Not in:**
- The passlib 1.7.4 / bcrypt 4.3.0 `__about__` warning from my repro. passlib
  traps it and it affects nothing here; it is a dependency-pin question for a
  separate issue.
- `hash_password`, `create_access_token`, `decode_access_token`, and the login
  route in `api/routes/auth.py`. The route already treats `False` as a failed
  login, so it needs no change.
- Any refactor of `core/security.py` beyond the one function.

## Files

- `core/security.py` — `verify_password` only.
- `tests/unit/test_security.py` — remove the marker, add one test.

There is no `pyproject.toml` suppression tied to #72 (checked), so nothing
else changes.

## Approach

1. Wrap the call on line 37 in `try` / `except ValueError: return False`.
   passlib defines `UnknownHashError` as a subclass of `ValueError` (verified:
   `issubclass(UnknownHashError, ValueError)` is `True` for passlib 1.7.4), so
   one clause covers both routes. I'll add a short comment saying so, so the
   next reader doesn't think only the plain `ValueError` case was intended.
2. Update the docstring's `Returns:` line to say `False` also covers a stored
   hash that cannot be used.
3. Delete the xfail marker (`tests/unit/test_security.py:218-221`).
4. Add `test_verify_with_malformed_bcrypt_hash`, asserting
   `verify_password("password", "$2b$12$abc") is False`.

The right-password and wrong-password behavior stays exactly as it is.

## Test plan

These are my Unit 2 reproduction steps re-run against the change, each with
its expected result:

| Step | Before (`main`) | Expected after |
|---|---|---|
| `pytest --runxfail tests/unit/test_security.py -k wrong_hash_format -v` | `FAILED`, `UnknownHashError` | `1 passed` |
| `pytest tests/unit/test_security.py -k wrong_hash_format -v` (marker in force) | `1 xfailed` | `1 passed`, with no `xfailed` or `XPASS(strict)` |
| Repro script: the control hash plus the four stored values above | controls `True`/`False`; four raise | controls `True`/`False`; all four return `False` |
| `pytest tests/unit/test_security.py` (whole file) | green, with 1 xfailed | green, with 0 xfailed |
| `make lint`, `make typecheck`, `make test-unit` | — | all pass |

## Risks and unknowns

- **Python version.** I run Python 3.14.7, but CI and the mypy config target
  3.11, and I have no 3.11 installed. I'm not changing anything
  version-sensitive, but CI on the pull request is the first 3.11 run.
- **Catching `ValueError` is wider than the four cases I tested.** Any
  `ValueError` passlib raises while verifying would now return `False`
  instead of raising. For a password check, failing closed is the safe
  direction, but a genuine passlib bug of that type would be hidden as a
  failed login rather than surfacing as an error. I'm flagging this as a
  trade-off, not something I've resolved.
- **The four inputs are not exhaustive.** They are the forms I ran. I haven't
  tried to enumerate every malformed hash passlib could see.
- **Other exception types.** I have not seen passlib raise anything other than
  these two types for a bad stored hash. If one turns up during the build, I'll
  record it below rather than silently widening the `except`.

## Deviations

Nothing changed between the plan I posted and what I actually built; the plan held. The build touched the same two files with the same change: `try` / `except ValueError: return False` around the call in `verify_password`, the updated `Returns:` line in the docstring, the deleted `xfail(strict=True)` marker on `test_verify_with_wrong_hash_format`, and the new `test_verify_with_malformed_bcrypt_hash`. Nothing during the build called for a change. Every row of the test plan came out as expected: `--runxfail` went from `FAILED` to `1 passed`, all four stored values went from raising to returning `False` with the controls unchanged, and `make lint`, `make typecheck` and `make test-unit` passed.

One thing I checked rather than took on trust: the live plan-check said the marker sat at lines 217–220. I looked, and it is at 218–221, as this plan says, so the plan's line numbers held too.
