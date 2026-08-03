---
name: implement
description: "Implement a spec or ticket set through dependency-aware scheduling, TDD, per-ticket commits, iterative code review, and full-suite verification."
disable-model-invocation: true
---

# Implement

Complete every ticket in the provided spec or ticket set.

## Schedule the tickets

Read all tickets and their blocking relationships before starting. Run tickets
sequentially by default. Parallelize only tickets that have no dependency on one
another and no overlapping write surface. Give each parallel implementation
subagent an isolated branch or worktree; never let concurrent agents commit in
the same worktree.

Continue useful main-thread work while implementation subagents run. Wait only
when no independent work remains or their results are required to proceed.

## Run the ticket loop

For each ticket, follow this sequence: plan, implement, review, verify.

### 1. Plan

Before editing:

1. Read the complete ticket, acceptance criteria, dependencies, and relevant
   repository instructions.
2. Inspect the affected code, tests, interfaces, and existing patterns.
3. Resolve discoverable facts from the repository. Ask the user only about
   high-impact decisions that cannot be discovered.
4. Define the implementation approach, test scope, commit boundary, and exact
   diff or fixed point that the reviewer will inspect.

Do not begin implementation while a blocking decision remains unresolved.

### 2. Implement and commit

Use the `/tdd` skill where applicable. Work in independently testable vertical
slices and keep the change within the ticket's scope.

Run the ticket's focused tests and relevant typechecking before committing. Do
not commit while a known failure remains unexplained. Use the `$git-commit` skill
to stage only files belonging to the ticket, inspect the staged diff, and create
the commit. Exclude unrelated changes, secrets, caches, build output, and runtime
artifacts.

### 3. Review

Before every review request, rerun the ticket's focused tests. Request review
only when they pass.

Create a dedicated code-review subagent. Give it the complete ticket, acceptance
criteria, exact committed diff or fixed range, and the review brief required by
the `$code-review` skill. The reviewer reports findings; it does not modify the
implementation.

The main agent owns the review loop:

1. Evaluate every finding.
2. Fix valid findings.
3. Rerun the focused tests covering each fix.
4. Commit the fixes with `$git-commit`.
5. Request another review of the updated range.

Run at most three review rounds per ticket. If material findings remain after
the third round, stop and report them; do not mark the ticket complete.

### 4. Verify and complete

After the review loop is clear, run the full test suite once. Mark the ticket
complete only when its focused tests and the full suite pass and no material
review finding remains unresolved.

Report the ticket's commits, tests, review rounds, and any remaining concerns,
then continue with the next unblocked ticket.
