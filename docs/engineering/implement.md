Quickstart:

```bash
npx skills add mattpocock/skills --skill=implement
```

```bash
npx skills update implement
```

[Source](https://github.com/mattpocock/skills/tree/main/skills/engineering/implement)

## What it does

`implement` builds every ticket in a spec or ticket set. It schedules work from
the blocking edges, parallelizes only independent tickets with isolated write
surfaces, then drives each ticket through planning, TDD, commit, review, and
full-suite verification.

A ticket is not complete until its focused tests pass before review, its review
loop is clear, and the full suite passes. Review is capped at three rounds; any
material finding left after that is reported rather than silently accepted.

## When to reach for it

You invoke this by typing `/implement` — the agent won't reach for it on its own.

Reach for it once the work is written down as a spec or split into tickets and you're ready to turn that into code. If the spec doesn't exist yet, write it first — for that, use [to-spec](https://aihero.dev/skills-to-spec), or [to-tickets](https://aihero.dev/skills-to-tickets) to break a spec into tickets. If you just want to build something test-first without a full spec, drop to [tdd](https://aihero.dev/skills-tdd) directly.

## Pre-agreed seams

The idea `implement` runs on is the **seam** — the stable interface a feature is tested at, chosen before any code is written. It doesn't invent seams mid-build; it uses the ones already picked (during [to-spec](https://aihero.dev/skills-to-spec)) and writes tests against them via [tdd](https://aihero.dev/skills-tdd). Working at pre-agreed seams is what keeps the implementation honest: the tests target something durable, so the code underneath can move without the tests moving.

## The ticket loop

`implement` treats the ticket as the unit of ownership. Dependencies and
overlapping write surfaces keep tickets sequential; genuinely independent work
can run concurrently in isolated branches or worktrees.

After planning and TDD, the ticket is committed and handed to a dedicated
reviewer with an exact diff. The main agent owns the fixes and follow-up commits,
requesting at most three review rounds. A final full-suite run closes the ticket.

## Where it fits

`implement` is the build-and-review step at the end of the main chain:

```txt
grill-with-docs → to-spec → to-tickets → implement
```

Reach for it after the work has been specced and sequenced, not before. Its key
neighbours are [to-tickets](https://aihero.dev/skills-to-tickets), which produces
the tickets and their blocking edges, [tdd](https://aihero.dev/skills-tdd), which
drives each implementation slice, and [code-review](https://aihero.dev/skills-code-review),
which supplies the review discipline. When you're unsure which skill or flow
fits, [ask-matt](https://aihero.dev/skills-ask-matt) routes you.
