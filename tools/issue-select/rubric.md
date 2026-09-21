# Rubric: is this a good first issue?

Five required checks decide the verdict; two preferred checks only rank the
issues that survive. Every pass condition below is measured against the
bundle's stated capture date in eval mode, and against today in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| repo-not-archived | The `archived:` flag on the repo line of the repo-facts block. Live: the "This repository has been archived" banner across the top of the repo front page. | The repo is not archived. An archived repo is read-only and cannot take a pull request, so this fails regardless of how good the issue looks. | required |
| commits-recent | The dates of the five entries under "last 5 default-branch commits" in the repo-facts block, compared against the capture date. Live: the commit list reached from the commit count on the repo front page, compared against today. | Count the commits among the 5 listed that are dated within 90 days before the capture date — in live mode, within 90 days before today. Ignore any commit authored by a bot account unless that commit merges a human's pull request. Pass if the count is 2 or more. | required |
| ai-policy-permits | The "contribution policy" line in the repo-facts block. Live: `CONTRIBUTING.md` in the repo root or `.github/`, any contributor docs it links out to, a dedicated `AI_POLICY.md`, and the PR template. | The policy does not outright refuse AI-assisted contributions. A stated refusal to accept AI-generated code or documentation fails. Conditions (disclose AI use, personally understand the change, test it, human-review the output) pass, and so does discouragement short of refusal, because those are terms to work under rather than a closed door. Silence passes: most repos state nothing, and saying nothing is not a restriction. | required |
| unclaimed | The "this issue: assignees:" and "linked PRs:" entries, with each PR's state, in the repo-facts block, plus every claim comment in the thread read with its date. Live: the Assignees and Development boxes in the issue sidebar, plus the thread. | All three hold: assignees is none; no linked PR is in the `open` state; and no claim comment ("working on this", "can I take this", "I'd like to work on this") dated within 180 days of the capture date stands unanswered. A linked PR that is `closed` or `merged` is an abandoned or finished attempt, not a live claim. A claim older than 180 days is stale and does not block, especially where a maintainer has since invited takers. | required |
| scope-bounded | The issue body, the full comment thread with dates, the issue's open date, and the state of each linked PR in the repo-facts block. | The issue names one concrete misbehavior or change and the end state it expects, and none of the following is present: it describes itself as a tracking, umbrella, or meta issue, or lists separate features meant to ship as separate pull requests; a maintainer states the fix requires changes to core internals; the thread shows a design question still being argued that no maintainer has settled; the body is a one-line wish with no described behavior, or hides an unmade product decision; the issue has been open more than 2 years and already carries 2 or more `closed`, unmerged linked PRs, because repeated abandoned attempts are evidence the work is harder than its label suggests; the issue is a usage or support question rather than a change. Three things are explicitly ONE bounded change and never an umbrella: several edits that together deliver one coherent change (a new documentation page plus the existing pages that must point at it); a list of examples of the same defect, including a trailing "etc."; and a maintainer's list of suspected causes, or of optional follow-up ideas, for one reported problem. Those describe the extent of a single fix, not separate work items. Judge the size of the work asked for, not the polish of the writeup: a terse bug report with a clear expected behavior passes, and a maintainer filing the bug is a strong signal the scope is real. | required |
| maintainer-responsive | The "maintainer first-response sample" in the repo-facts block. Live: the first reply from a commenter badged Owner, Member, or Collaborator on a few recently updated issues. | At least one sampled issue drew a maintainer reply within 30 days. | preferred |
| shipped-recently | The "latest release" entry in the repo-facts block. Live: the Releases box in the repo front page sidebar. | A release is dated within 365 days of the capture date. A repo that has published no releases at all does not fail this check; `commits-recent` already carries liveness. | preferred |

## Verdict rule

Accept only if every `required` check passes. A single required failure is a
reject. `preferred` checks never change the verdict: they rank the accepted
issues against one another, and an accepted issue that fails one is still
accepted. Treat `unclear` as a fail on required checks, because a first issue
whose evidence cannot be verified is not one to take; on preferred checks
treat `unclear` as neutral, so missing evidence lowers an issue's rank without
rejecting it.
