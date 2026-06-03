# WhyAskingMe

A decision-support skill for Claude Code. At any fork, instead of dumping
half-formed options on you or picking the first plausible path by reflex, it
plans every option to comparable depth, ranks them on merit, and either decides
the call itself or — when running autonomously — executes the best one and
verifies it.

## The problem

At a decision point, coding agents waste your attention two ways:

- **They offload the choice.** You get a list of options with no real analysis
  behind them and have to pick blind.
- **They pick by reflex.** The agent commits to the first approach that comes to
  mind, often a workaround that masks a symptom or a path that ignores how the
  codebase already does things.

Neither is a *decision*. This skill turns a fork into a reasoned one: the options
are enumerated (including the easy-to-miss ones), each is planned to the same
depth, and the best is chosen on criteria that actually matter — not on a
made-up time estimate.

## What it does

When Claude reaches a fork — competing implementations, a bug fix vs. a deeper
cause, a "which approach" call — the skill runs one repeatable move:

1. **Enumerate the real options**, including do-nothing, root-cause vs.
   workaround, and reuse vs. build. No false binaries.
2. **Pre-plan each to equal depth** — mechanism, steps, scope, reversibility, how
   it would be verified, risk.
3. **Score against a quality bar**: root cause over symptom, simplest thing that
   works, reuse existing patterns, verifiable, reversible — **never by
   calendar-time guesses.**
4. **Pick the best and say why**, then **decide it** (if it is a reversible
   technical call) or **ask** (only if it is irreversible or genuinely your
   product/preference call), with the options already evaluated.

It is built to make **goal-directed, autonomous work** better: under a `/goal`
condition or in auto mode (`--permission-mode auto`), it executes the recommended
option and verifies it instead of stalling for a prompt — while still refusing to
take an irreversible, one-way-door action unsupervised.

## Install

The skill is the `skills/why-asking-me` directory — plain Markdown, no scripts,
no dependencies.

```bash
git clone https://github.com/fangshuor/WhyAskingMe.git
cp -r WhyAskingMe/skills/why-asking-me ~/.claude/skills/why-asking-me
```

Or per-project, to travel with one repository:

```bash
cp -r WhyAskingMe/skills/why-asking-me /path/to/your/project/.claude/skills/why-asking-me
```

Claude Code consults it automatically at decision and planning moments — that is
what the `description` in `SKILL.md` is for. You can also invoke it directly with
`/why-asking-me`.

## Scope

This skill does one thing: help Claude make the best choice at a fork. It
deliberately does **not** track context-window or usage limits — that belongs in
a separate, focused skill so this one stays pure decision support.

## Layout

```
WhyAskingMe/
└── skills/
    └── why-asking-me/
        ├── SKILL.md
        └── references/
            ├── decision-framework.md    # reversibility, when to ask, auto-mode limits, root-cause
            └── evaluating-options.md    # planning options to equal depth, the quality bar, verification
```
