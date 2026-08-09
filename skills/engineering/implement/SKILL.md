---
name: implement
description: "Implement a spec or ticket set through dependency-aware scheduling, explicit per-ticket verification maps, selective TDD, bounded code review, predeclared milestone gates, and one final full-suite verification."
disable-model-invocation: true
---

# Implement

Complete every ticket in the provided spec or ticket set.

## Build the execution graph

Read all tickets, acceptance criteria, dependencies, repository instructions,
and overlapping write surfaces before editing.

Define:

- ticket dependency order;
- independently executable tickets;
- the base commit of each ticket;
- milestone boundaries and their included tickets;
- the final repository gates.

Run tickets sequentially by default. Parallelize only tickets with no dependency
or overlapping write surface, using isolated branches or worktrees.

A blocked ticket blocks its dependents, not unrelated tickets.

## Build the verification map

Before implementing each ticket, map every acceptance criterion to:

- the behavior or invariant being proved;
- the public seam under test;
- the exact focused check or command;
- whether it requires real dependencies;
- any milestone gate that must prove it later.

Classify checks by behavioral scope, not by technology or runtime cost:

- **focused** — proves behavior owned by one ticket; it may use a real database,
  queue, browser, container, or Production Image when that is the lowest
  sufficient seam;
- **milestone** — proves a predeclared behavior spanning multiple tickets;
- **final** — runs all repository-required suites against the final candidate.

A complete integration, acceptance, browser, or category suite is not focused
merely because it is the easiest available command. Select only the tests mapped
to the ticket.

Declare milestone tickets and exact commands before implementation. Do not
invent or promote a ticket to a milestone after a test or review failure.

Assign each required behavior to one tier. Do not repeat the same proof at
multiple tiers.

## Select the implementation mode

Record one implementation mode in the ticket plan.

Invoke `$tdd` when:

- the user, ticket, or approved specification explicitly requires test-first or
  red-green development;
- a reproducible bug or regression has a pre-agreed public seam, an independent
  expected result, and a tight focused loop;
- high-risk concurrency, transaction, retry, state-machine, security, or
  algorithmic behavior has the same seam, oracle, and tight-loop properties.

Once invoked, follow `$tdd` unchanged.

Do not invoke `$tdd` merely because:

- production code changes;
- tests are required;
- an integration or E2E check will run;
- a missing symbol, fixture, or import can produce a RED result.

Use characterization-first verification for behavior-preserving refactors and
migrations. Otherwise implement directly and add the smallest focused evidence
that proves the ticket.

## Run the ticket loop

For each ticket, follow: plan, implement, review, verify.

### 1. Plan

Before editing:

1. Read the complete ticket and acceptance criteria.
2. Inspect the affected code, tests, interfaces, and existing patterns.
3. Resolve discoverable repository facts.
4. Record:
   - implementation mode and reason;
   - scope and commit boundary;
   - base commit;
   - verification map;
   - milestone-dependent criteria.

Do not begin while a blocking decision remains unresolved.

### 2. Implement and commit

Work in independently testable vertical slices and stay within ticket scope.

During development, run the smallest useful checks. When the ticket change is
coherent, run every mapped focused check once against the candidate commit.

Do not run milestone or final gates before review passes.

Do not commit with an unexplained failure. Prove that a failure is unrelated
against an unchanged baseline or equivalent evidence before classifying it as
unrelated.

Use `$git-commit` to stage only ticket files, inspect the staged diff, and commit
the change. Exclude unrelated changes and runtime artifacts.

### 3. Review

After focused checks pass, invoke `$code-review` with the ticket's base commit
and approved specification.

### 4. Verify the ticket

Bind every passing result to the tested commit and its mapped behavior.

A later code change invalidates only evidence whose tested seam, fixture, build,
or runtime contract was affected. Do not invalidate an entire test tier merely
because some code changed. When uncertain, run the narrowest affected check.

After review passes, rerun only invalidated focused checks.

Check off only criteria supported by passing evidence. If milestone-dependent
criteria remain, leave the ticket incomplete and record it as ready for its
predeclared milestone.

Report:

- commits;
- implementation mode;
- focused commands and durations;
- review rounds and findings;
- fixes and invalidated evidence;
- deferred criteria and blockers.

## Run milestone gates

Run a milestone only when:

- it was declared before implementation;
- every included ticket has passed review;
- every included focused check passes;
- the exact candidate commits are recorded.

Before an expensive gate, run a seconds-level, read-only readiness check for
required configuration, ports, processes, and health endpoints.

Building images, starting services, running migrations, seeding data, and
creating fixtures are environment setup, not preflight. Use the repository's
required isolated test environment and clean it afterward.

Run the milestone command once against its candidate.

If it fails:

1. Preserve the failure evidence.
2. Diagnose with the smallest command that reproduces the failure.
3. Stop a long gate when failures are cascading from a demonstrated common
   cause; do not wait for every related timeout.
4. Reopen the owning ticket for a code failure.
5. Fix, run affected focused checks, and review the changed ticket range.
6. Rerun only the failed or invalidated milestone checks.

An environment failure against unchanged commits does not invalidate unrelated
passing evidence.

After a milestone passes, check off its criteria and complete the included
tickets that now have no unverified criteria.

## Run the final gate

Run all repository-required suites after:

- every ticket has passed review;
- every milestone has passed;
- every acceptance criterion is verified;
- no material finding remains.

If the final gate fails, diagnose with the smallest failing check before making
changes. A code fix reopens its owning ticket and requires focused verification
and review of the changed range.

After the fix, run failed checks first, then run one clean final gate against
the new final candidate.

Complete the ticket set only when the final gate passes. Report commits, checks
by tier and duration, review rounds, and remaining blockers.