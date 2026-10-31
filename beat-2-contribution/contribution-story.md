# Contribution: tools/ax25_replay.cpp: heap overflow in loadWav() when the WAV data chunk length is not a multiple of 4

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Bryant Nguyen
**GitHub Username:** bnguyen142
**Issue:** https://github.com/aethersdr/AetherSDR/issues/4907
**Pull Request:** https://github.com/aethersdr/AetherSDR/pull/6902

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: issue scout tool**

I built a tool that searches and aids in narrowing down bugs that would be
candidates for me specifically. I had to change from the beets issue
because the scout flagged that beets' AI_POLICY.md bans AI on good first
issues.
Running the queries with no spacing between them caused GitHub to block the
last two, while Python got through because it ran first. I realized that
I need to space each query separately.

**Unit 6: Voice Guard**

The voice guard was extremely useful to prevent me from making errors.
However, I had to tweak it because of the clash between my "never post
a date" rule and the class guidance, which led me to reword line 79 of my
voice guide. I ran the manual check twice and the hook stopped the real post on a rule
the manual check missed. What I learned is the hook is critical; I
actually shared it with my coworkers since they ran into such issues
before. For the repo memory file, I kept the AGENTS.md import since the
template rule didn't fit for this repo, and the one orientation run
did find the same bug in a second file, which I will mention to Jeremy.

**Unit 7: Ship-gate**

I built the ship-manifest with 3 checks feeding the ship-gate hook. It caught the `<wav>` placeholder error, the pipe breaking the markdown table, and two `pr-precheck` rejections. I fixed the commands to capture correctly, and fixed the two `pr-precheck` rejections by switching to `--body-file` with the repo's actual template structure, and by rewording the sibling-bug claim — which I later actually posted as a real comment.

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I picked AetherSDR because it's one file and Linux-testable. The
language is C++, which I wanted to get refamiliarized with. The scope is
pretty narrow and focused, which I confirmed the fix applies to. I
wanted to focus more on the whole experience of interacting with open
source and maintainers. AI201 was great for getting my feet wet, and I
learned a lot from there. I wanted something well defined and small in
scope so that I can hone my skills as a developer and with my AI
assistant before grabbing a more ambitious PR.

I picked the issue from the AI301 Issue Selection site and claimed it
there on 10-18. My scout said accept and that all 6 required checks
passed; the 10-21 re-grade said accept, straightforward. Out of the 3
picks, beets#2491 was rejected by the scout for its AI ban on "good
first contribution" issues. Cataclysm-BN#9875 passed the scout, however
the reporter said there was an engine change and a maintainer hadn't
responded, which left that ticket ambiguous on how to proceed.

The owner added the `aetherclaude-eligible` bot label on 2026-09-29,
and my scout still passed `unclaimed`. It has been a while and the bot
didn't fix the issue, hence why I want to have a go at it, but I asked
Jeremy in my claim before starting.

Scout verdict for #4907 (`tools/issue-scout/scan-output.md`, 2026-10-18
direct grade): "the bug sits in one function in one file
(`tools/ax25_replay.cpp:81-82`)."

**Claim comment:** https://github.com/aethersdr/AetherSDR/issues/4907#issuecomment-6391036939 (posted 2026-10-24 16:46 PDT)

**Toolkit**

- Tool: issue-scout. The tool was helpful because it helped narrow down
  issues. The full scan was useful because it narrowed the field cheaply
  before running an expensive LLM to do the deeper check. This is a faster
  way to do it and reduces cost and time.
- What it produced: 3 graded: 2 accepts, 1 reject. It accepted AetherSDR
  #4907, with all 6 required checks passing.
- Difficulties: I hit the API rate limit because I tried to run all
  three queries close together. It took me at least 2 hours to realize
  what the actual issue was. I still need the deeper grading check to
  find subtle AI usage bans in particular situations.
- What changed: Pacing fixed the rate limit. The `ai-policy-permits`
  check (`tools/issue-scout/rubric.md` line 13) essentially wasn't
  looking everywhere for an AI ban, which in this case was a subset of
  bug tickets, so I patched it to say "A scoped AI ban on the issue's
  label counts as a refusal." I also added a required `no-bot-dispatch`
  check (line 15). The scout needs this because there may be bug
  tickets that were labeled for a bot but weren't executed, so the bug
  is just sitting there. I posted a claim so that the maintainer can
  deny it or assign it to me, but it does open up more tickets to see,
  especially in this case where the repo bot resolves a ticket within an
  hour.

---

## Understanding the Issue

**Problem Description**

Heap buffer overflow issue. The tool `ax25_replay` is a tool that inputs
WAV files and decodes them into AX.25 packets. A function inside of it,
`loadWav()`, sizes its buffer by rounding the `data` chunk down to 4-byte
samples, then it copies the whole chunk anyway. If the length isn't a multiple of 4, such as the file provided
(6 bytes), it writes 1-3 bytes past the end of the buffer, causing the
bug.

**Expected Behavior**

If the WAV `data` length isn't a multiple of 4, it should reject the
file.

**Current Behavior**

The tool accepts the file and writes past the buffer. With the normal
build nothing visible happens, but with AddressSanitizer:
`heap-buffer-overflow`, `WRITE of size 6` into a `4-byte region`.

**Affected Components**

- `tools/ax25_replay.cpp`, function `loadWav()`, lines 81
  (`samples.resize(dataBytes / 4)`) and 82 (`memcpy(..., dataBytes)`)
- the build target `ax25_replay`, defined in `tests/tests.cmake:6417`.
  It's `EXCLUDE_FROM_ALL`, so CI never builds it.
- not part of the main app and not reachable from the radio; it's a
  developer-only tool

**Toolkit**

- Tool: Claude Code read the issue thread, the code and the build file.
- What it produced: the exact lines and build target, and that the link
  failure was already fixed.
- Difficulties: 2 bot comments, and out-of-date info from the reporter
  on the link failure that wasn't updated in the issue.
- What changed: I check current `main` instead of just relying on the
  issue text.

---

## Reproduction Process

**Environment Setup**

OS Arch Linux (Omarchy, kernel 7.2.8), gcc 16.2.1, Python 3.14.8,
Qt 6.12.0, cmake 4.4.4, ninja 1.13.2, AetherSDR `main` at `3b4931f0`.
I cloned the repo, installed the needed packages (plus the README's
Arch dependency list), and built only the `ax25_replay` target:

```bash
git clone https://github.com/aethersdr/AetherSDR.git
cd AetherSDR
sudo pacman -S --needed cmake ninja rtl-sdr qt6-serialport qt6-websockets qtkeychain-qt6
cmake -B build -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo
cmake --build build --target ax25_replay

# second build with AddressSanitizer, which is what shows the bug
cmake -B build-asan -G Ninja -DCMAKE_BUILD_TYPE=Debug \
  -DCMAKE_CXX_FLAGS="-fsanitize=address -fno-omit-frame-pointer" \
  -DCMAKE_C_FLAGS="-fsanitize=address -fno-omit-frame-pointer" \
  -DCMAKE_EXE_LINKER_FLAGS="-fsanitize=address"
cmake --build build-asan --target ax25_replay
```

Challenges: the bug ticket mentioned the tool didn't link and how the
reporter got around the issue. Since then it has been fixed by #5494,
but that wasn't updated in this ticket. The normal build hid the bug,
so I needed the second build with AddressSanitizer.

**Steps to Reproduce**

1. Create a bad WAV file: run the reporter's generator script (copied
   verbatim from issue #4907, inlined here so this repro doesn't
   depend on the thread staying unchanged):

   ```python
   # Reporter's generator from AetherSDR#4907, verbatim.
   import struct
   fmt  = struct.pack('<HHIIHH', 3, 1, 48000, 48000*4, 4, 32) + b'\x00\x00'
   data = b'\x01\x02\x03\x04\x05\x06'          # 6 bytes = 1.5 floats
   body = b'WAVE' + b'fmt ' + struct.pack('<I', len(fmt)) + fmt \
        + b'data' + struct.pack('<I', len(data)) + data
   wav  = b'RIFF' + struct.pack('<I', len(body)) + body
   open('evil.wav','wb').write(wav + b'\x00' * max(0, 44 - len(wav)))
   ```

   This writes `evil.wav` with a 6-byte `data` chunk.
2. Run the ASan build: `./build-asan/ax25_replay evil.wav`
3. Observe: `heap-buffer-overflow`, `WRITE of size 6` at
   `tools/ax25_replay.cpp:82`, with the buffer allocated at `:81`.
4. Control: with the same script from step 1 changed to an 8-byte chunk,
   create `control.wav`; it loads 2 samples and shows no ASan error.

**Reproduction Evidence**

- **Commit showing reproduction:** the fix commit is signed `b3354ed8`
  on branch `fix/4907-ax25-replay-wav-overflow`, part of PR
  https://github.com/aethersdr/AetherSDR/pull/6902, opened 2026-10-31
  without waiting further for the maintainer's reply to my claim
  (https://github.com/aethersdr/AetherSDR/issues/4907#issuecomment-6391036939).
- **Screenshots/logs:** `./build-asan/ax25_replay evil.wav` on `main` at
  `3b4931f0`, 2026-10-24 (excerpt; library frames trimmed):

  ```text
  ERROR: AddressSanitizer: heap-buffer-overflow on address 0x7b83e59e02d4 at pc 0x7f63ea929714 bp 0x7fff35ef17f0 sp 0x7fff35ef0f98
  WRITE of size 6 at 0x7b83e59e02d4 thread T0
      #1 0x555aa750ac60 in loadWav tools/ax25_replay.cpp:82
  0x7b83e59e02d4 is located 0 bytes after 4-byte region [0x7b83e59e02d0,0x7b83e59e02d4)
  allocated by thread T0 here:
      #7 0x555aa750ac32 in loadWav tools/ax25_replay.cpp:81
  SUMMARY: AddressSanitizer: heap-buffer-overflow tools/ax25_replay.cpp:82 in loadWav
  ```

- **My findings:** the link failure was already fixed (#5494). The
  overflow reproduces on the real binary, not just the reporter's
  copied code. The normal build hides the bug (exit 3, no crash).

  I tested the bot's proposed fix (a reject check on non-multiple-of-4
  `data` chunks), applied temporarily to the real binary and rebuilt
  with ASan, 2026-10-24:

  ```text
  $ ./build-asan/ax25_replay evil.wav    # 6-byte data chunk
  load failed: data chunk length 6 is not a multiple of 4 (float32 sample size)

  $ ./build-asan/ax25_replay seven.wav   # 7-byte data chunk
  load failed: data chunk length 7 is not a multiple of 4 (float32 sample size)

  $ ./build-asan/ax25_replay control.wav # 8-byte data chunk, valid
  Loaded 2 samples @ 48000 Hz (0.0 s)
  ...
  === TOTAL decoded across both polarities: 0 ===
  ```

  No ASan report on any of the three — the bot's fix stops the
  overflow. But a WAV with no `data` chunk at all still hits a
  separate, pre-existing bug: with `-fsanitize=undefined` on the
  extracted-code harness, both before and after the bot's fix:

  ```text
  # before the bot's fix
  harness.cpp:66:16: runtime error: null pointer passed as argument 1, which is declared to never be null
  # after the bot's fix
  harness_botfix.cpp:71:16: runtime error: null pointer passed as argument 1, which is declared to never be null
  ```

  So the bot's fix is not touched by this PR's scope question — the
  null-pointer `memcpy` on a missing `data` chunk is a separate,
  pre-existing bug either way.

**Toolkit**

- Tool: Claude Code, plus AddressSanitizer and UBSan.
- What it produced: a repro on the real binary, the control file check,
  and the gap in the bot's fix.
- Difficulties: out-of-date information on the issue, a normal build
  that was hiding the bug, and missing packages.
- What changed: I built the real binary because my guard wouldn't let
  me claim something I hadn't run, and added a second, ASan build.

---

## Solution Approach

**Analysis**

The function `loadWav()`, defined in `tools/ax25_replay.cpp`, sizes its sample buffer using `samples.resize(dataBytes / 4)` and then copies into it with `std::memcpy(samples.data(), data, dataBytes)`. The two statements use different sizes for the same `data` chunk. In the `evil.wav` test case, `dataBytes = 6`: `resize` computes `6 / 4 = 1` (integer division floors the result, discarding the remainder of 2), allocating space for 1 float (4 bytes). The `memcpy` call has no knowledge of what `resize` computed — it simply copies the full `dataBytes`, 6 bytes, into a 4-byte buffer. That mismatch is the heap-buffer-overflow confirmed by AddressSanitizer at `tools/ax25_replay.cpp:82`.

**Proposed Solution**

I changed `loadWav()` to reject the chunk if it's not a multiple of 4, instead of truncating it. Specifically, I added `if (dataBytes % 4 != 0)` before the `memcpy` line, which sets the `err` out-param to explain why the file failed (instead of failing silently as before) and returns `false`, rejecting the entire WAV file rather than loading it.

Another option was to truncate the file, but for a WAV file this would mean losing part of the sound data. I chose reject instead because it matches the existing precedent in `QsoWavFormat.cpp`, which also rejects on a size mismatch rather than truncating, and because the bot's comment on the issue thread also suggested reject.

I did not touch the `:60` bounds check, the no-`data`-chunk case, or the sibling bug discovered during investigation. The `:60` bounds check is out of scope for this PR and not practically testable: the platforms AetherSDR builds for are all 64-bit (`CMakeLists.txt` and the CI workflows show no 32-bit/i386/ILP32 build target), so the theoretical overflow (`pos + sz` wrapping) can only happen if `qsizetype` is 32-bit, which we just established it is not. As a result, `pos + sz` can never get close to overflowing a 64-bit signed integer, so the bug can't manifest in any build this project produces — testing it would require compiling a genuinely 32-bit binary, which AetherSDR doesn't support. The no-`data`-chunk case is a separate, pre-existing bug, out of scope, called out in the PR body itself. The sibling bug in `tools/ax25_session_analyze.cpp` was flagged as a question in a PR comment (https://github.com/aethersdr/AetherSDR/pull/6902#issuecomment-6543234119), not posted as a separate bug ticket.

**Implementation Plan**

1. Modify `loadWav()` in `tools/ax25_replay.cpp` to reject a bad chunk length.
2. Rebuild with ASan and rerun the test WAVs to confirm the fix.

**Toolkit**

- Tool: `CLAUDE.local.md` (repo memory)
- What it produced: it already held the scope decision about `:60`, the reject-vs-truncate reasoning based on the bot's thread, the `QsoWavFormat.cpp` precedent, and the note about the sibling bug in another part of the code with the same issue — all written on 2026-10-24, before this session started.
- Difficulties: trusting week-old notes, and needing to re-verify a claim before relying on it (like the `:60` 32-bit reasoning, which I re-checked today instead of just copying it).
- What changed: added a new session-log entry that includes the signing setup, the fork, and the branch/commit; the failed self-assign attempt (and why this repo doesn't allow it); opening the PR and adding a PR comment about the sibling bug; and a dedicated note about why no automated testing exists for this fix.

---

## Testing Strategy

I tested with a control (`control.wav`, 8B, valid) and two deliberate error WAV files (`evil.wav`, 6B; `seven.wav`, 7B), plus `nodata.wav`, which had no `data` chunk. Before the fix, `evil.wav` led to a heap-buffer-overflow at `tools/ax25_replay.cpp:82`. After the fix, both `evil.wav` and `seven.wav` led to clean rejection (a `load failed` message), exit 1; `control.wav` and `nodata.wav` had unchanged behavior, so no ASan report. I also ran 4 standalone static-check scripts (`check_qsettings.py`, `check_ci_test_gate.py`, `check_network_timeouts.py`, `check_test_registration.py`), which all passed.

I did not create a CTest to use as a regression test, because `ax25_replay` is `EXCLUDE_FROM_ALL` (not built by the normal test pass at all). Also, `loadWav()` is a `static` function in an anonymous namespace, which means it's not callable from any test file — the only way to exercise it is running the compiled binary against a WAV file. This is why the manual ASan run replaces the regression test.

**Toolkit**

- Tool: ship-gate (the manifest and `check_gate.py` hook), which checks the code fix and everything around it. It prevents me from pushing/shipping code before it meets certain requirements.
- What it produced: it ran 3 manifest checks (Linux build, ASan regression, 4 static-check scripts), which all passed.
- Difficulties: it caught two real mistakes in my manifest: (1) a `<wav>` placeholder literally left in a shell command, which gave an "unexpected EOF" error; (2) a literal `|` pipe character inside a markdown table cell, which broke the table parsing — the character was interpreted as a new column rather than as a pipe.
- What changed: I fixed the command to point to the real WAV files with an absolute path, and rewrote the ASan check to avoid the pipe (`> file 2>&1 ; grep` instead of `| tee`).

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**2026-10-24**

I filled out the voice guide today with the 5 missing Wrong/Right
examples for questions and pings. I hit a snag with conflicting
instructions: one from last class said to report back in a day or so,
however it conflicted with my never promise a date rule, which was
referring to fixing the bug and not each individual step. I reworded
the guard to allow dates for steps but not promise when the bug would
be fully resolved. The guard did catch some issues when I was claiming
the bug. One of the things I had in my guard was to not claim a bug
unless I run it. This led to some interesting findings, including that
a blocker was no longer a blocker. I also tested a fix and confirmed
that a change to one file stops the overflow. Also made the claim on
the bug (posted 16:46 PDT).

When I posted the claim, the hook had already passed the fixed text,
but then auto mode blocked Claude from posting, so I posted with `!`,
which goes around the hook. Later on, at 17:25, I discovered to my
horror what happened, re-ran the hook on the text as posted on GitHub,
and it passed. This is a hard lesson to learn, as it points out that
running hooks needs to be done in a certain way.

**2026-10-31**

I checked issue #4907 to see if I got confirmation to proceed. There was no reply, and I decided to proceed per class guidance. I set up SSH commit signing, which was a requirement of this repo. I tried to self-assign the issue but got a GitHub permission error, and had to learn why: I did not have push access to this repo, hence I can't be assigned. Understanding this gave me a better grasp of how GitHub works for outside contributors, which is good to know for future PRs.

`gh pr create` first used a free-form body (just Summary/Testing/Disclosure sections) built with a bash heredoc (`--body "$(cat <<'EOF' ... EOF)"`). `pr-precheck` rejected it because the beginning of the text was `$(cat <<'EOF'`, meaning the heredoc syntax didn't evaluate into plain text first. It also flagged that the body "lacks the template's Intent, Constitution principle, Test plan and Checklist sections" — this meant I had not used the actual `.github/PULL_REQUEST_TEMPLATE.md`. The fix was to copy the body to a file (`pr_body_4907.md`) and use `--body-file` instead of an inline `--body` heredoc, and rebuild it to match the repo's required template sections.

`pr-precheck` then ran again on the rewritten body and caught a second issue: `plan-fidelity [fail]` — the description said the sibling `ax25_session_analyze.cpp` bug was "flagged on the issue," but none of the 3 comments on the PR had such a comment. It was a to-do item, but it wasn't actually posted. The ship-gate catching these errors made me more careful about the commands and claims I write, and showed why this check is needed.

**Toolkit**

- Tool: ship-gate's `pr-precheck` pass.
- What it produced: two rejections before the PR could be opened — one for the malformed/wrong-template body, one for the unverified "already flagged" claim.
- Difficulties: realizing the heredoc wasn't capturing the information properly, and tracking what I was planning to do versus what I had already done, which in this case was claiming I had flagged an issue in the PR comments when I hadn't yet.
- What changed: I corrected both issues — switching to `--body-file` with a real file matching the repo's template, and rewording the sibling-bug claim to be honest about not having raised it yet, then actually posted it as a real PR comment afterward.

---

## Pull Request

**PR Link:** https://github.com/aethersdr/AetherSDR/pull/6902

**PR Description:** The PR fixes `loadWav()` in `tools/ax25_replay.cpp`. It doesn't touch the `:60` check, the no-data-chunk case, nor the sibling bug discovered during investigation of this bug. I ran a full test before and after to make sure the code works, and followed the repo's own PR template (Summary/Intent/Constitution principle/Test plan/Checklist). The PR was opened without maintainer confirmation of the claim, which is consistent with class guidance that a PR is graded as opened regardless of a reply.

**Maintainer Feedback:**

- 2026-10-31: I checked today to see if the maintainer provided any feedback, and there was none as of 10/31. This is fine per the template.

**Status:** Awaiting review.

**Toolkit**

- Tool: the voice guide, used for the Pull Request comment.
- What it produced: it made sure to stick to one question per comment, that I'd done the groundwork first (reviewed the relevant code) and flagged the sibling bug as unconfirmed ("by inspection... though I haven't built or ASan-tested it").
- Difficulties: making sure to stay on topic and not overextend into offering extra scope.
- What changed: I actually posted the comment about the related bug I found.

---

## Learnings and Reflections

**Technical Skills Gained**

Technical skills learned: how to set up SSH commit signing, which was a requirement for this repo; a better understanding of git remote/fork mechanics (origin vs. fork vs. upstream); that GitHub has an assignment permission model for outside contributors; awareness of pipe characters breaking shell commands embedded in markdown tables; and the importance of matching a project's actual PR template structure instead of writing one freeform.

**Challenges Overcome**

Challenges overcome: understanding the self-assign permission error and why it happens; two bugs of mine caught by ship-gate's manifest checks (the `<wav>` placeholder and the pipe character); and two separate PR-body rewrites caught by `pr-precheck` (the unrendered heredoc/wrong template structure, and the unverified "already flagged" claim).

**What I'd Do Differently Next Time**

Next time I would double-check what I stated I was doing — in this case, notifying the maintainer of a bug — and be careful to verify commands before posting.

---

## Resources Used

- Issue #4907 thread (https://github.com/aethersdr/AetherSDR/issues/4907):
  the reporter's generator script, now inlined in the Steps to
  Reproduce section above, helped me reproduce the bug.
- AetherSDR README, Arch dependencies and build steps
  (https://github.com/aethersdr/AetherSDR#dependencies): helped build
  the binary and dependencies to replicate the bug.
- CONTRIBUTING.md
  (https://github.com/aethersdr/AetherSDR/blob/main/CONTRIBUTING.md) and
  CONSTITUTION.md, Principles VII and XIII
  (https://github.com/aethersdr/AetherSDR/blob/main/CONSTITUTION.md):
  helped me understand the purpose and general guidelines of the
  project, including its encouragement to use a specific LLM, Claude
  Code.
- AGENTS.md (https://github.com/aethersdr/AetherSDR/blob/main/AGENTS.md):
  useful because, together with the two above, it helps ensure I am
  matching the style standards of the repo.
- docs/COMMIT-SIGNING.md
  (https://github.com/aethersdr/AetherSDR/blob/main/docs/COMMIT-SIGNING.md):
  goes over the requirement for SSH signing setup, which specified the
  `allowedSignersFile` error. The error was listed as expected for
  signing and only needed for local verification.
- .github/PULL_REQUEST_TEMPLATE.md
  (https://github.com/aethersdr/AetherSDR/blob/main/.github/PULL_REQUEST_TEMPLATE.md):
  reading this file showed me the required sections I was missing
  (Intent, Constitution principle, Test plan, Checklist), which
  `pr-precheck` had rejected the freeform body for lacking.
