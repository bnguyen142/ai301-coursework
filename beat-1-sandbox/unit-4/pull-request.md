# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/120

**Branch**

`fix/72-verify-password-fail-closed`

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Calibration checks, unscored: I ran the 4 calibration packages with `--only` before any full run (calib-01 alone, then calib-02, calib-03, calib-04). All 4 matched their gold labels; calibration packages are never scored, so they have no agreement score.
2. Full run 1: **20/20** (`agreement: 20/20 scored items  (bar: 18/20: PASS)`), with a single `repo-standards` check covering template, issue link, AI disclosure and maintainer direction.
3. Full run 2, after splitting `repo-standards` into `repo-template`, `ai-disclosure` and `maintainer-direction` (applying my Unit 3 feedback that one check was doing several jobs): **19/20**. This is the run I committed in `eval-run.txt`: `agreement: 19/20 scored items  (bar: 18/20: PASS)`. The one miss, pkg-16, is covered under Trade-offs.

**Package analysis**

**pkg-20** (standards-wall), from the committed run 2. My rubric decided **reject**; the gold label is **reject**.

Two required checks failed. By my procedure's rule (quote the first failing required check in rubric order), the quoted one is `test-evidence`: "Repro before/after shown, but 'zig build test passes (config suite included)' has no output, and the conditional-pair control is claimed without output." The check that matches the package's category also failed, `ai-disclosure`: "AI_POLICY.md requires disclosing tool and extent of assistance; the description contains no AI disclosure." The package's repo facts say: "All AI usage in any form must be disclosed, stating the tool used and the extent of the assistance", and the PR's description has no disclosure at all. The code itself was fine: `plan-fidelity` passed ("Diff touches only src/config/Config.zig and its test block, matching the plan's files") and `clean-diff` passed ("Diff contains only the changeConditionalState fix and its tests"). `repo-template` and `maintainer-direction` also passed, so the split shows exactly which rule tripped: in run 1 the same failure was reported under the bundled `repo-standards` check.

The code was fine, but the repo has a stated rule that AI use must be disclosed, and the PR ignores it completely. A PR that breaks the repo's own stated rules isn't ready to submit, no matter how good the code is, because a maintainer would send it back. Ghostty's rule also asks for the tool and the extent of AI help, which is more specific than the general disclosure I used on my own PR #120.

**Check rationale**

From `tools/pr-precheck/rubric.md`, exactly as it reads now:

> | plan-fidelity | The diff and the description, read against the plan context (its steps, files, and not-in-scope) | Pass if the diff does what the plan said it would do, or any difference is explained in the description. Fail if there's a difference and it isn't explained. A description that claims the plan was followed when the diff shows otherwise counts as unexplained. | required |

I started from my Unit 3 `narrow-scope` row, which asked whether the change "stays narrowly on the reported bug." I rejected that framing here: at the PR stage the question is whether the diff matches the plan, not whether it is small. A wide change the plan announced is not drift, and a tiny change the plan never mentioned is.

My first version stopped at "Fail if there's a difference and it isn't explained." I then added the last sentence for the case where the description says the plan was followed but the diff shows otherwise. A false claim is not an explanation, but the grader is a model, so I said it out loud instead of leaving it implied. calib-03 is that case: its description says the change follows the plan "exactly, with no changes beyond it", while the diff rewrites `printPropertyKey` in `src/language-js/print/property.js`, outside the plan's files. My tool rejected it on `plan-fidelity` with: "Diff rewrites printPropertyKey in src/language-js/print/property.js, outside the plan's files and scope, while the description claims 'exactly, with no changes beyond it'."

**Trade-offs**

The check that gives something up is `test-evidence`. Its pass condition ends: "Fail if there's no proof, or it only says "tests pass" without showing output."

In run 1 it failed in all 13 reject packages, 9 of them outside the not-tested category, but never failed a clear-accept. In run 2, with `test-evidence` unchanged, it failed in 11 of the 13 rejects and also failed one clear-accept, pkg-16, which flipped that package to reject and is my only miss (19/20). The two runs read pkg-16 the same way and decided differently. Run 1 passed it: "go test outcome is only stated, noted as thin." Run 2 failed it: "the repo's checks appear only as "`go test ./pkg/minikube/machine/... ./cmd/...` passes" with no output." pkg-16 shows a real before/after and a control, so the gold label accepts it; only the suite result is claimed without output. That is the borderline my rule sits on, and the grader landed on different sides of it in two runs.

What it gives up: it can block a good PR over a small gap, and it often becomes the quoted reason ahead of the real problem (in pkg-20 the quoted reason is the missing test output, not the missing AI disclosure).

I'm keeping it strict because I'd rather set the bar high. A PR whose proof can't be seen isn't ready to submit, and I'd rather hold a borderline PR and ask for the output than let a weak one through. The cost is one miss, and it still leaves me over the bar. Before run 2 I named this exact case as the one to watch ("a PR that clearly ran its tests but didn't paste every line of output"), and pkg-16 is it. If I loosened it, the change would belong in the pass condition, for example passing a stated suite result when the before/after proof is shown, followed by canaries on the not-tested packages and a full run.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
