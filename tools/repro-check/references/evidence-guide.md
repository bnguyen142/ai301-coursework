# Evidence guide: where proof lives in a reproduction package

Every check in `rubric.md` names evidence. This file says where to find it.

A package has five parts, the same in every bundle: `## Repo facts`,
`## Issue`, `## Thread highlights`, `## Candidate claim comment`, and
`## Candidate repro report`. Live mode has the same five in different places:
the repo's own pages and docs, the issue page, its thread, and the student's
draft comments.

Read the issue first. Every question below is finally answered against it: the
issue is the thing the package claims to have reproduced.

## Environment

**Where it lives.** In the bundle: the environment record inside
`## Candidate repro report` — usually its first paragraph or an **Environment**
heading — read against the version, platform and install method stated in
`## Issue`. The `- bug reports:` line under `## Repo facts` names what the
project's own template asks a reporter to state, which is the project's own
definition of a sufficient record. Live: the draft comment against the issue
body, plus the repo's issue template.

**What good looks like.** The record names the operating system, the version of
the tool or library the issue concerns, and the versions of the dependencies
the failure runs through — anything the issue's traceback names counts as such
a dependency. Where any of those differ from what the issue states, the report
says so in words, rather than leaving the reader to compare two lists and
notice. A record that satisfies the project's own `bug reports` template is
sufficient by definition; one that omits a field that template asks for is not.

## Steps

**Where it lives.** In the bundle: the preparation and execution portions of
`## Candidate repro report` — the input files it shows, the commands it gives,
and the order they appear in. Live: the same parts of the draft.

**What good looks like.** The steps start from a state the reader can reach on
their own (a clone, an install, or a file whose full contents are shown) and
end at a command the reader can run verbatim. Input is given literally, not
described: a file's contents are pasted, not summarised. Nothing important
happens off-screen — "set the project up as usual", or a command whose flags
are paraphrased, leaves the reader guessing. Length is not the measure: a
two-line repro can be complete, and a ten-step one can skip the step that
matters.

## Behavior shown

**Where it lives.** In the bundle: the artifacts pasted inside
`## Candidate repro report` — output excerpts, tracebacks, error messages,
logs — read against the behavior `## Issue` describes, including any traceback,
exception name or message the issue quotes. Live: the draft's pasted output
against the issue body.

**What good looks like.** The artifact shows the same failure the issue names:
the same exception type, the same message, or the same observable result. A
failure of a different kind is not a match even when it comes from the same
command and the same file — a parse error is not a panic, and an import error
is not a wrong return value. A test that merely passed, was skipped, or was
reported as expected-to-fail shows nothing on its own, because the suite
swallows the failure; the artifact has to show the error actually surfacing. A
report with no artifact at all, however confidently written, shows nothing.

## Honesty

**Where it lives.** In the bundle: every assertion in `## Candidate claim
comment` and `## Candidate repro report`, held against the artifacts the
package actually contains. Live: the same, against what the draft pastes.

**What good looks like.** Each claim about what happened is backed by something
shown. Where the report says it ruled out a cause — its own environment,
branch, input or configuration — it names how, so the reader can see the
failure belongs to the code the issue names rather than to the reporter's
setup. A report that says it could not reproduce is honest and complete when it
shows the steps it ran and the output it got instead; that outcome is a pass,
not a failure. What fails is a claim the package does not back: "confirmed",
"rigorous", "deterministic", or a second run on another version asserted with
no output to show for it.

## Comms

**Where it lives.** In the bundle: the full text of `## Candidate claim
comment`, read against `## Issue` and against the `- contribution policy:` and
`- bug reports:` lines under `## Repo facts`; `## Thread highlights` shows what
maintainers and other commenters have already said. Live: the draft comment
against the issue page, `CONTRIBUTING.md` in the repo root or `.github/`, any
dedicated AI policy file, and the pull-request or issue templates.

**What good looks like.** The claim names this issue's specifics — the
function, file or behavior it is about — rather than being a sentence that
would fit any issue. It states what the author is going to investigate and
stops there: no delivery date, no deadline, and no promise of a particular fix,
because neither is knowable before the work is done. Where the contribution
policy explicitly requires disclosing AI assistance, one of the comments says
plainly that AI was used; where it explicitly refuses AI-generated
contributions, no comment reports having used AI. A policy that says nothing
about AI, or that discourages it without refusing it, imposes nothing. Where
the project's `bug reports` template asks for particular fields, the report
supplies them.
