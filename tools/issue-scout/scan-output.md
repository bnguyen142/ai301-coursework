# issue-scout scan output

## 2026-10-18: full scan (`issue-scout: run a scan`)

Q1 (Python) ran at about 12:35 PM PDT and returned 100. Q2 (C++) and Q3 (C)
were blocked by the secondary rate limit, then re-run at about 12:53 PM PDT,
30 seconds apart: Q2 returned 100, Q3 returned 31.
Combined 231, 0 duplicates. Filter 1 (drop assigned) left 231; filter 2
(drop more than 3 comments) left 160 (Q1 79, Q2 67, Q3 14). Ordered by
`updatedAt`, newest first. All top 5 came from Q1.

**Accepted** (fit order)

1. **santosh-sankranthi/falkordb-py#6**, "Handle empty query results gracefully". Q1, rank 3 (updated 2026-10-18 17:21, 3 comments).
   - **Fit:** Python. The expected behavior is a 3-item list that includes "Add a test case". Tests run with pytest against a FalkorDB Docker image.
   - **Tension:** the repo is a personal fork created today (2026-10-18 14:50) of FalkorDB/falkordb-py, with 0 stars. All 6 of its issues were opened today. The owner's replies read like a knowledge-graph bot ("Based on the knowledge graph..."). A PR here would go to a sandbox, not the real project. The rubric passes it; it is not a real first contribution.
2. **searxng/searxng#6835**, "Google engine returns HTTP 403". Q1, rank 2 (updated 2026-10-18 17:30, 3 comments).
   - **Fit:** Python, large active project, runs in Docker on Linux.
   - **Tension:** the cause is Google blocking SearXNG's requests. No maintainer has commented, and a code fix may not exist. AI_POLICY.rst says AI must disclose, must not be "the main author of the PR", and issues and PR descriptions must be fully human-written.
3. **new-usemame/Calibre-Web-NextGen#2647**, "KOReader sync failing after update". Q1, rank 5 (updated 2026-10-18 16:15, 2 comments).
   - **Fit:** Python/Flask, very active, released today.
   - **Tension:** reproducing needs a Kobo or KOReader device plus the Readest phone app. The reporter asks "Did identifiers change?", so it may be user-side.
4. **new-usemame/Calibre-Web-NextGen#2649**, "changing language or theme does not change anything". Q1, rank 4 (updated 2026-10-18 16:15, 1 comment).
   - **Tension:** front-end settings bug (the profile says avoid front end). No version or config given; the template fields are left as examples.

**Rejected**

5. **kettleofketchup/DraftForge#293**, "admins can't signup for the events". Q1, rank 1 (updated 2026-10-18 19:11, 2 comments). Sunk by **unclaimed**: the owner opened PR #295, which is open, 5 minutes after filing the issue. The issue body is also the unfilled template.

```json
[
  {
    "item": "https://github.com/santosh-sankranthi/falkordb-py/issues/6",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "archived=false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "5 of 5 commits dated 2026-10-18 by dudizimber (inherited from upstream FalkorDB/falkordb-py; fork created 2026-10-18)"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "no CONTRIBUTING or AI policy; AGENTS.md and CLAUDE.md present (written for AI agents)"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; no PR references; no claim comments"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "'Return an empty result when no records match... Add a test case'; owner points to QueryResult.__parse_records (falkordb/query_result.py:474)"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "owner replied to #6 one minute after it was opened (17:18 -> 17:19)"},
      {"name": "shipped-recently", "grade": "pass", "evidence": "no releases published; does not fail"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/searxng/searxng/issues/6835",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "archived=false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "4 human commits 2026-10-16..17 (inetol, Bnyro); dependabot commit ignored"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "AI_POLICY.rst: disclose, fully understand, AI never the main author, human-written issues and PR text (conditions, not a ban)"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; no PR references; 3 comments are +1 reports"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "one misbehavior, expected 'The Google engine returns search results'. Tension: cause is Google blocking, fix may not exist"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "#6832 opened 2026-10-11 04:05, return42 (MEMBER) replied 08:10"},
      {"name": "shipped-recently", "grade": "pass", "evidence": "no releases published; does not fail"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/new-usemame/Calibre-Web-NextGen/issues/2647",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "archived=false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "5 of 5 commits dated 2026-10-18 by new-usemame"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "CONTRIBUTING.md states no AI rule; AI_README.md is written for AI assistants"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; no PR references; owner comment is an intake ack ('on the tracker'), not a claim"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "one misbehavior with steps, expected 'Progress should be synced'"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "#2602 opened 2026-10-14, owner replied 2026-10-16"},
      {"name": "shipped-recently", "grade": "pass", "evidence": "release published 2026-10-18"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/new-usemame/Calibre-Web-NextGen/issues/2649",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "archived=false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "5 of 5 commits dated 2026-10-18 by new-usemame"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "CONTRIBUTING.md states no AI rule; AI_README.md is written for AI assistants"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; no PR references; owner comment is an intake ack"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "'changing language or theme does not change anything', expected 'changes should be applied'"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "#2602 opened 2026-10-14, owner replied 2026-10-16"},
      {"name": "shipped-recently", "grade": "pass", "evidence": "release published 2026-10-18"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/kettleofketchup/DraftForge/issues/293",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "archived=false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "5 of 5 commits dated 2026-08-30..2026-10-18 by kettleofketchup"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "CONTRIBUTING.md 'AI Use': AI tools may be used; code must be reviewed"},
      {"name": "unclaimed", "grade": "fail", "evidence": "open PR #295 by owner, 2026-10-18 19:15, 5 minutes after the issue"},
      {"name": "scope-bounded", "grade": "fail", "evidence": "body is the unfilled bug template; only 'Can't signup without adding yourself' in a comment"},
      {"name": "maintainer-responsive", "grade": "unclear", "evidence": "not sampled; owner files and fixes their own issues"},
      {"name": "shipped-recently", "grade": "pass", "evidence": "no releases published; does not fail"}
    ],
    "verdict": "reject"
  }
]
```

---


## 2026-10-18: grade of the 2 issues added by the new ranking rule

Re-ranked the same scan results with the new filter 4 (top 3 from Q1, top
1 from Q2, top 1 from Q3). Q2's slot went to memgraph #471 and Q3's to
rizin #6908, replacing Calibre-Web-NextGen #2647 and #2649.

**Accepted**

1. **memgraph/memgraph#471**, "Fix min int value throws error". Q2 top (updated 2026-10-18 02:35, 3 comments).
   - **Fit:** C++ with a Python client repro from a maintainer (`pymgclient`). One parser bug, labeled `Effort - Low`, and runs in Docker on Linux.
   - **Tension:** open since 2022-07-22. One earlier attempt, PR #3960 (2026-03-25), was closed unmerged because its author hadn't signed the CLA; they wrote "The fix approach is sound if someone with a signed CLA wants to pick it up." Memgraph requires a CLA. The bug was not checked against current master. The maintainer response sample found no maintainer reply on 6 recent issues.

**Rejected**

2. **rizinorg/rizin#6908**, "Macros are not serialized on `Ps` to the project". Q3 top (updated 2026-10-18 07:15, 1 comment). Sunk by **ai-policy-permits**: CONTRIBUTING.md says "Using AI tools to fix issues labelled as 'good first issues' is forbidden". Also fails **unclaimed**: Master-Zero1 asked to take it on 2026-10-18, with no answer yet.

```json
[
  {
    "item": "https://github.com/memgraph/memgraph/issues/471",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "archived=false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "5 of 5 commits dated 2026-10-15..16 by humans (mattkjames7, Ignition, colinbarry)"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "CONTRIBUTING.md and PR template state no AI rule (silence passes); CLA required"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees none (gitbuda unassigned 2024-03-14); PR #3960 closed unmerged; no claim comments"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "'RETURN $i = -9223372036854775808' errors 'Integer literal exceeds 64 bits'; expected the min int value; open 4 years but only 1 closed PR (rule needs 2+)"},
      {"name": "maintainer-responsive", "grade": "unclear", "evidence": "no maintainer reply found on 6 issues opened before 2026-10-12"},
      {"name": "shipped-recently", "grade": "pass", "evidence": "v3.13.2 released 2026-10-09"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/rizinorg/rizin/issues/6908",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "archived=false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "5 of 5 commits dated 2026-10-18 by humans"},
      {"name": "ai-policy-permits", "grade": "fail", "evidence": "CONTRIBUTING.md: 'Using AI tools to fix issues labelled as good first issues is forbidden'"},
      {"name": "unclaimed", "grade": "fail", "evidence": "Master-Zero1 2026-10-18: 'I would like to work on this as my first contribution', unanswered"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "maintainer Rot127 filed it with expected vs actual output: macros lost after Ps/Po"},
      {"name": "maintainer-responsive", "grade": "unclear", "evidence": "not sampled"},
      {"name": "shipped-recently", "grade": "pass", "evidence": "v0.9.1 released 2026-06-29"}
    ],
    "verdict": "reject"
  }
]
```

---

## 2026-10-18: direct grade of the 3 Issue Selection site picks

Command: `issue-scout: grade https://github.com/beetbox/beets/issues/2491 https://github.com/cataclysmbn/Cataclysm-BN/issues/9875 https://github.com/aethersdr/AetherSDR/issues/4907`

**Accepted**

1. **aethersdr/AetherSDR#4907**: heap overflow in `loadWav()`, found via direct URL.
   - **Why it fits:** the bug sits in one function in one file (`tools/ax25_replay.cpp:81-82`). It comes with an ASan trace and a Python script that builds the bad WAV file. The maintainer's bot proposed a fix, and the end state is clear: the two buffer sizes must come from one expression. That lets you write the test first and work back to the fix. It also builds and tests on Linux, and the code is C++ you can read.
   - **Things to watch:**
     - **Your PR may race their bot.** On 2026-09-29 the owner (ten9876) added the `aetherclaude-eligible` label. That authorizes the project's own AI agent to take the issue. The rubric doesn't treat a label as a claim, so the issue passes `unclaimed`. But 19 days later no PR exists, so ask on the issue before you start.
     - **The tool may not build.** The issue says `ax25_replay` doesn't link. PR #5494, merged 2026-09-08, may have fixed that; check it.
     - **Commits must be signed.**
     - **The tool is excluded from the default build,** so you have to build it on purpose.
   - **AI policy:** CONTRIBUTING.md "strongly encourages" using Claude Code.

**Rejected**

2. **beetbox/beets#2491** (Python, the best match for the profile): sunk by **ai-policy-permits**. The repo's `AI_POLICY.md` says: *"AI is not for 'good first contribution' issues."* This issue has the `good first issue` label. The rubric's wording is about refusing AI across the repo, and this ban covers only one class of issue. Treated as a refusal for this issue. Every other required check passed.
3. **cataclysmbn/Cataclysm-BN#9875**: **passes all five required checks, but skip it.**
   - The typo is still on main at `collapsed_tower.json:79,165` (`"x": [48, 74]`).
   - The reporter also says the fix needs changes to `mapgen.cpp`. A reporter saying that doesn't trip scope-bounded; only a maintainer saying it does. In three months the issue has drawn no comments at all, so nobody has confirmed that half.
   - It's C++ and JSON game data, with a crash only reproduced on Windows. That's a weak fit.

   The rubric decides, so the verdict stays accept. Ranked below AetherSDR.

```json
[
  {
    "item": "https://github.com/aethersdr/AetherSDR/issues/4907",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "isArchived: false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "5 of 5 commits dated 2026-10-18 by humans (jensenpat, Ozy311, on8st, on8st, chibondking)"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "CONTRIBUTING.md: 'We strongly encourage all contributors to use Claude Code'"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; no linked or mentioned PRs; no claim comments. Tension: owner added aetherclaude-eligible label 2026-09-29, authorizing the project bot"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "one resize/memcpy mismatch at tools/ax25_replay.cpp:81-82 (still on main); maintainer bot: 'a few lines in one file'; reject-vs-truncate settled by recommendation plus owner's eligible label"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "#6207 opened 2026-10-05 16:11, ten9876 replied 16:12; #6219 answered within a day"},
      {"name": "shipped-recently", "grade": "pass", "evidence": "v26.10.3 released 2026-10-18"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/cataclysmbn/Cataclysm-BN/issues/9875",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "isArchived: false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "5 of 5 commits dated 2026-10-18 by humans"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "contributing.md: disclose AI use, add Assisted-by trailer, human-written PR summary (conditions, not a ban)"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; no linked or mentioned PRs; zero comments"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "one crash, expected 'generating the collapsed tower terrain without crashing'; claim that mapgen.cpp needs changes is the reporter's, not a maintainer's; typo still at collapsed_tower.json:79,165"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "#10631 opened 2026-10-17 12:13, WishDuck (maintainer) replied 17:52 same day"},
      {"name": "shipped-recently", "grade": "pass", "evidence": "v0.13.0 released 2026-09-25"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/beetbox/beets/issues/2491",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "isArchived: false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "5 of 5 commits dated 2026-10-02..2026-10-09 by snejus and JOJ0"},
      {"name": "ai-policy-permits", "grade": "fail", "evidence": "AI_POLICY.md: 'AI is not for \"good first contribution\" issues'; this issue is labeled good first issue"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees none; no PRs; 2026-02-03 comment is a +1, not a claim"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "maintainer sampsyo: 'reasonable to show the album that the duplicate track is a part of'; open 9 years but 0 closed PRs"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "#7037 opened 2026-09-16, snejus replied 2026-09-17"},
      {"name": "shipped-recently", "grade": "pass", "evidence": "v2.14.1 released 2026-09-17"}
    ],
    "verdict": "reject"
  }
]
```
