---
name: implement
description: "Implement a spec or ticket set through dependency-aware scheduling, risk-based verification, per-ticket commits, bounded code review, milestone gates, and final full-suite verification."
disable-model-invocation: true
---

# Implement

Complete every ticket in the provided spec or ticket set.

## Build the execution and verification graph

Read all tickets, acceptance criteria, dependencies, repository instructions,
and overlapping write surfaces before starting.

Define:

- ticket dependency order;
- independently executable tickets;
- milestone boundaries;
- the verification tier of every required check:
  - **focused** — ticket-local tests, lint, typechecking, or acceptance probes;
  - **milestone** — expensive integration, browser, database, service, or
    cross-ticket checks;
  - **final** — the full test suite.

Assign an expensive check to one tier. Do not repeat it at another tier unless a
later code change invalidates its result. The full suite is a final gate, not a
per-ticket gate.

Run tickets sequentially by default. Parallelize only tickets with no dependency
or overlapping write surface, using isolated branches or worktrees.

A blocked ticket blocks its dependents, not unrelated tickets.

## Run the ticket loop

For each ticket, follow: plan, implement, review, focused completion.

### 1. Plan

Before editing:

1. Read the complete ticket and acceptance criteria.
2. Inspect affected code, tests, interfaces, and existing patterns.
3. Resolve discoverable facts from the repository.
4. Define the implementation approach, scope, focused checks, commit boundary,
   review fixed point, and any milestone-dependent acceptance criteria.

Do not reinterpret every implementation ticket as a request for TDD.

Invoke `$tdd` only when the user, ticket, or `$tdd` skill's own trigger makes it
applicable. Once invoked, follow `$tdd` unchanged. Merely needing tests does not
by itself require TDD.

When `$tdd` does not apply, implement directly and add or update the smallest
targeted evidence needed to verify the ticket.

Do not begin while a blocking decision remains unresolved.

### 2. Implement and commit

Work in independently testable vertical slices and stay within the ticket scope.

During development, run only the smallest useful checks. When the ticket change
is coherent, run its planned focused checks.

Do not commit with an unexplained failure. Classify a failure as unrelated only
after proving it against an unchanged baseline or equivalent evidence.

Use `$git-commit` to stage only ticket files, inspect the staged diff, and commit
the change. Exclude unrelated changes and runtime artifacts.

### 3. Review

Before requesting external review, perform a review-stage quality pass over the
complete diff. Check:

- acceptance-criteria coverage;
- module ownership and interface boundaries;
- error and state transitions;
- concurrency and transaction behavior;
- compatibility and migration behavior;
- unnecessary duplication or complexity.

Fix known problems, rerun affected focused checks, and commit the result.

Create a dedicated read-only review subagent. Give it the complete ticket,
acceptance criteria, and exact committed diff or range. The reviewer reports
findings and must not modify files or operate shared runtime services.

Every review request counts as one round.

After each round:

1. Evaluate all findings.
2. Record the rationale for rejected findings.
3. Fix valid findings as one coherent batch.
4. Rerun only affected focused checks.
5. Commit the fixes.
6. Request another review only if required.

Use at most three review rounds. If material findings remain after round three,
mark the ticket blocked and continue only independent tickets. Never start a
fourth round.

A passing focused result remains valid while its reviewed commit is unchanged.

### 4. Complete the ticket

After review passes, rerun only checks invalidated by changes made since their
last passing result.

Mark the ticket complete when:

- its focused acceptance criteria pass;
- the reviewed fixed point is unchanged;
- no material review finding remains.

Leave milestone-dependent acceptance criteria unchecked until their milestone
gate passes.

Report commits, focused checks, review rounds, deferred criteria, and blockers.

## Run milestone gates

Run a milestone gate only after every included ticket has passed review.

Before starting an expensive gate:

1. Confirm the exact commits being tested.
2. Check required services, ports, permissions, and resources.
3. Assign one owner for shared runtime operations.

If a milestone gate fails:

- diagnose before rerunning;
- reopen the responsible ticket when the failure is caused by code;
- fix and review the changed range;
- rerun only failed or invalidated milestone checks.

An environment failure against unchanged commits does not invalidate unrelated
passing checks. Required acceptance remains incomplete until the environment
check succeeds.

## Run the final gate

Run the full suite once after:

- all tickets have passed review;
- all milestone gates have passed;
- no material finding remains.

If it fails, diagnose the failure before rerunning.

A code failure reopens its owning ticket and requires focused verification plus
review of the changed range. After the fix, run the failed checks first, then
run one clean full suite at the new final fixed point.

Complete the ticket set only when every required criterion and gate passes.
Report commits, checks by tier, review rounds, and remaining blockers.