# Decision framework

Depth behind the "Decide, ask, or execute," "Auto mode," and "Don't rabbit-hole"
sections of the skill.

## Reversibility classes

Reversibility sets how much caution a decision deserves, and how freely an
autonomous run may act on it. Most coding decisions are far more reversible than
they feel in the moment.

**Reversible — decide and proceed (auto mode may execute freely).** The cost of
being wrong is a follow-up edit.

- Any change tracked in version control and not yet pushed.
- Internal refactors, renames, file moves.
- A library or pattern choice that is swappable later.
- Test scaffolding, formatting, comment wording.
- Local experiments you can delete or rewind.

**Reversible but expensive — decide, but record what you chose and why.**
Undoable, yet undoing wastes real work, so leave a trail.

- A migration applied to a local or disposable database.
- A dependency upgrade that touches many call sites.
- A broad mechanical change across many files.

**Irreversible or externally visible — ask first; auto mode pauses or takes the
safe path.** The cost of being wrong is borne outside the session or cannot be
undone.

- Force-pushing over shared history; rewriting published commits.
- Dropping or altering real data; destructive migrations.
- Publishing a release, package, or public artifact.
- Rotating or revoking credentials; changing access.
- Anything that sends data to a third party, or money.
- Deleting or overwriting files you did not create and cannot reconstruct.

## The always-ask list

Independent of reversibility, these need the user because the agent cannot supply
the answer from the state in front of it:

- A genuine product or priority trade-off (which feature matters more, what the
  user values, what the experience should be).
- Missing external information or secrets only the user has (an API key, a
  business rule, the intended behavior when the spec is silent).
- A choice that contradicts an instruction the user gave earlier.

Everything outside this list and outside the irreversible class is yours to
decide — after you have actually evaluated the options.

## What auto mode may execute on its own

In a `/goal` run or auto mode, the default is to act, not to wait. The boundary:

- **Execute now:** anything in the reversible class, once you have picked it on
  merit and have a way to verify it.
- **Take the safe path instead:** when the best option is irreversible but a
  reversible variant keeps progress (branch instead of force-push; soft-delete or
  back up before destructive change; stage a release without publishing), take the
  reversible variant and note it.
- **Pause and surface:** when there is no safe reversible path and the action is
  destructive, externally visible, or on the always-ask list. Present the
  evaluated options and your recommendation, then continue once answered.

Momentum is the goal, but not at the cost of a one-way door taken unsupervised.

## Annotating options when you ask

When a call is genuinely the user's, the question should carry the work you have
already done. State each option, its plan, its cost and risk, and your pick.

**Template:**

```
<Decision in one line>.

Option A — <name>: <scope/size>, <reversibility/risk>. <one-line consequence>.
Option B — <name>: <scope/size>, <reversibility/risk>. <one-line consequence>.

Recommendation: <A or B>, because <reason from the quality bar — root cause,
simplicity, reuse, verifiability, reversibility>.
```

**Example — a genuine product call, already evaluated:**

```
When an upload fails halfway, two behaviors are defensible and I cannot pick for
you because it is a product call:

Option A — discard the partial upload and have the user retry. Small, reversible,
no new infrastructure. Simpler experience, more rework on flaky networks.
Option B — resume from the last good chunk. Medium; needs a server-side session
store we do not have yet. Better experience, more moving parts to maintain.

Recommendation: A first — it is the simplest thing that works and is reversible;
B is a clean follow-up if resumable uploads turn out to matter. Which do you want?
```

The cost of annotating is a few sentences. The benefit is a decision made on
facts and far fewer clarification rounds — and in interactive mode it often
converts an "ask" into a "decide and proceed," because once the options are laid
out the right one is obvious.

## Root-cause discipline

Workaround loops are the most expensive failure at a fork: a symptom is patched,
the patch shifts the symptom, and the run burns turns going in circles while the
real defect sits untouched.

1. **Reproduce.** Get the failure to happen on demand and read the actual error
   and stack, not a paraphrase. A bug you cannot reproduce, you can only guess at.
2. **Isolate.** Narrow to the smallest input or code path that still fails.
   Shrink the search space before theorizing.
3. **Hypothesize once, then test it.** State the most likely cause in one sentence
   and test that specific claim. Do not change several things at once.
4. **Fix the cause, not the symptom.** Prefer the change that makes the failure
   impossible over the one that makes this instance pass. If a symptom patch is
   genuinely necessary under pressure, label it and record the real cause.

**The stop rule.** Two workarounds for the same symptom without resolution means
you are in a loop. Stop, and either restart the analysis from step 1 with what the
failed attempts taught you, or, if the cause needs information you do not have,
ask the user for that one specific thing rather than guessing a third time.
