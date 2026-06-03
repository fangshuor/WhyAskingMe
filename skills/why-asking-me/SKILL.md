---
name: why-asking-me
description: >-
  Decision-support skill for Claude Code. Use this whenever you are about to
  present the user with a choice between options, reach a fork in how to
  implement or fix something, choose among approaches in a plan, or make any
  judgment call while working toward a goal. Instead of dumping raw options on
  the user or picking by reflex, it enumerates the real options (including the
  easy-to-miss ones), pre-plans each to comparable depth, scores them against a
  real quality bar — does it fix the root cause or only a symptom, is it the
  simplest thing that works, does it reuse existing patterns, can it be verified,
  is it reversible — never by calendar-time guesses, then recommends the best with
  reasoning and decides reversible technical calls itself rather than asking.
  Under a /goal run or auto mode it executes the best option directly and verifies
  it instead of waiting for a prompt, reserving only genuinely irreversible or
  product/preference choices for the user, presented with the options already
  evaluated. Trigger it proactively at any decision point, option-presenting,
  planning, fork, or "which approach" moment, even if the user never asks for help
  deciding.
---

# why-asking-me

Coding agents waste the user's attention two ways at a fork: they dump a set of
half-formed options and make the user choose blind, or they pick the first
plausible path by reflex. This skill replaces both with a small, repeatable move
— work out each option properly, pick the best on merit, and then either decide
it yourself or, when running autonomously, just do it and verify.

The name is the test to run on yourself: before you hand the user a choice, ask
*why am I asking?* If you could work out the right answer from what is in front
of you, work it out. The user's time is for the decisions only they can make.

This is an aid to goal-directed work (`/goal`, auto mode): every fork an
autonomous run hits becomes a reasoned, verified decision instead of a stall or a
coin flip.

## When this runs

Any decision point: a fork in how to implement or fix something, a set of options
you were about to present, competing approaches in a plan, a "which way should we
go" judgment call. Match effort to stakes — a typo fix or an obvious one-way path
needs none of this; a fork with real consequences gets the full pass below.

## The flow

1. **Enumerate the real options.** List the genuine candidates, including the
   ones easy to skip past: doing nothing, fixing the root cause versus a
   workaround, reusing an existing pattern versus building new, a smaller-scoped
   variant. A false binary ("A or B?") when there is a better C is the most
   common failure here.

2. **Pre-plan each option to comparable depth.** For each, work out: what it
   actually does, the concrete steps, its scope and reversibility, how you would
   verify it worked, the main risk, and the downstream consequence. They have to
   be planned to the *same* depth or the comparison is rigged. If you are unsure
   of an option, explore first — and push that exploration into a subagent so it
   does not bury your main context (see `references/evaluating-options.md`).

3. **Score against the quality bar — not by calendar time.** Rank by what
   actually makes a choice good: does it address the root cause rather than mask a
   symptom; is it the simplest thing that works rather than over-built; does it
   reuse existing patterns; can it be verified with a test, build, or check; is it
   reversible and appropriately scoped. Never rank options by "which is faster in
   days/weeks" — a session has no calendar, and a time estimate hides the things
   that matter. Details in `references/evaluating-options.md`.

4. **Pick the best and say why** in a line or two: why it wins and why the
   runners-up lose. That sentence is what makes the decision reviewable.

5. **Then decide, ask, or execute** — by who owns the call and what mode you are
   in.

## Decide, ask, or execute

Sort the chosen call on two axes: is it **reversible** (undoable cheaply — a
tracked code change, a refactor, a swappable choice) or not (a force-push over
shared history, dropped data, a release, a credential change); and is it a
**technical** call with a defensible best answer or a **product/preference**
call only the user holds.

- **Reversible and technical → it is yours.** Interactive: state the decision and
  the one-line reason and proceed — do not stage it as a question. Autonomous:
  execute it.
- **Irreversible, externally visible, or genuinely the user's → the user's.**
  Present the already-evaluated options plus your recommendation (use
  `AskUserQuestion`), so they decide on facts, not vibes.

The short list of things always worth asking about, and the reversibility
classes, are in `references/decision-framework.md`.

## Auto mode (autonomous runs)

When you are running under a `/goal` condition, in auto mode
(`--permission-mode auto`), or the user has said to run autonomously, **do not
wait** — that is the whole point of those modes. Run the flow above, execute the
recommended option, then **verify it**: run the test, the build, or the check and
read the result; show the evidence rather than asserting success. A verified
best-decision at every fork is exactly what lets an unattended run finish
correctly.

Autonomy is for momentum on reversible work, not for walking through one-way
doors unsupervised. So even in auto mode, if the best option is irreversible,
externally visible, or destructive, prefer the safest reversible path that keeps
progress, or pause and surface it — rather than doing something that cannot be
undone without a human. Everything reversible: just do it and keep moving toward
the goal.

## Don't rabbit-hole

The decisions above go wrong most often by chasing a symptom in circles. Before
choosing a workaround, find the cause: reproduce the failure, read the actual
error, form one hypothesis, test it, then fix the cause rather than the symptom.
If you have tried two workarounds for the same symptom without resolving it, stop
— you are in a loop; switch to root-cause analysis or surface the specific
missing information. And resist the opposite failure: do not over-engineer the
winning option with defensive layers and tests for cases that cannot happen. The
full discipline is in `references/decision-framework.md`.

## Reference files

Read these when you need the depth; the body above carries the common case.

- `references/decision-framework.md` — reversibility classes, the always-ask
  list, what auto mode may and may not execute on its own, option-annotation
  templates, and the root-cause debugging discipline.
- `references/evaluating-options.md` — how to pre-plan and compare options to
  equal depth, the quality bar in full, building a verification check for the
  winner, and using subagents to explore options and to adversarially confirm the
  chosen one.
