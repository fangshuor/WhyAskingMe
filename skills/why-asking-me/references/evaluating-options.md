# Evaluating options

Depth behind steps 1–4 of the skill: how to plan options to comparable depth and
rank them on merit. These criteria are drawn from what actually separates a good
agentic decision from a bad one.

## Plan each option to equal depth

A comparison is only honest if every option is worked out to the same level. The
common trap is planning your preferred option in detail and the alternatives in a
sentence, which guarantees the favorite wins. For each candidate, work out:

- **What it does** — the actual mechanism, not a label.
- **The concrete steps** — enough to see the real shape of the work.
- **Scope** — how much it touches (a quick `git diff`-level sense for code, or
  which files/systems are involved).
- **Reversibility** — can it be undone cheaply if it is wrong.
- **Verification** — how you would prove it worked (see below).
- **Main risk and downstream consequence** — what it makes harder later.

Explore before you plan when you are unsure of an option. Separating research from
the decision keeps you from committing to the wrong approach. Push the exploration
into a subagent (below) so reading ten files to scope one option does not bury the
context you need to decide.

## The quality bar

Rank options against these — in roughly this order when they conflict:

1. **Root cause over symptom.** An option that removes the cause beats one that
   masks it, even if the mask is smaller. A symptom patch that will resurface
   under a different input is not actually cheaper.
2. **Simplest thing that works.** Prefer the least machinery that satisfies the
   real requirement. Extra abstraction, defensive layers, and tests for
   impossible cases are costs, not virtues. Do not pad the winning option with
   them either.
3. **Reuse existing patterns.** An option consistent with how the codebase already
   does things beats a novel one of equal power — it is easier to verify, review,
   and maintain. Look at a comparable feature before inventing.
4. **Verifiable.** Prefer an option you can prove with a test, a build, a linter,
   or a screenshot over one whose correctness you can only assert.
5. **Reversible and appropriately scoped.** Between two otherwise-equal options,
   take the one that is easier to undo and touches less.

**Never rank by calendar time.** "Option A takes two days, B takes a week" is a
fiction in an agentic session — there is no calendar, and the estimate hides the
things that decide quality. If you catch yourself comparing options by how long
they would take in days or weeks, throw that axis out and compare them on the
criteria above.

## Build the winner a verification check

The single thing that most improves an autonomous decision is giving the chosen
option a check it can run, because "looks done" is otherwise the only stopping
signal. Before executing, decide what proves it worked:

- a test (ideally one that fails first and passes after the change),
- a build or type-check exit code,
- a linter,
- a script that diffs output against a fixture,
- a screenshot compared against a target.

Then run it and show the evidence — the command and what it returned — rather than
asserting success. Under a `/goal` run this is what the evaluator re-checks every
turn; in a single pass, run the check and iterate in the same flow.

## Use subagents to explore and to verify

Subagents run in their own context and report back a conclusion, which makes them
the right tool at two points in this flow:

- **Exploring an option.** "Investigate how X works, find the files and existing
  utilities I could reuse, and report the smallest safe plan — do not edit
  anything." You get the scoped plan without the dozen files it took to find it.
- **Verifying the winner.** After executing, have a subagent review the diff in a
  fresh context against the requirement: "review this change for correctness and
  edge cases; report only gaps that affect correctness or the stated requirement,
  not style." A fresh model that did not write the code grades it more honestly —
  and tell it to flag only real gaps, so the review does not manufacture
  over-engineering.

Brief them self-containedly: they have none of your context, so give the paths,
the goal, and what "done" looks like, and ask for the conclusion rather than a
narration of every step.
