---
name: tickets-review
description: Review a generated ticket set for spec coverage, tracer-bullet granularity, dependency correctness, and agent readiness.
disable-model-invocation: true
---

# Tickets Review

Audit a proposed or published ticket set before implementation begins. Apply the
same tracer-bullet contract used to generate agent-ready tickets.

This is a review skill. Do not edit or publish tickets unless the user explicitly
asks for those changes after seeing the findings.

## Process

### 1. Establish the review basis

Read:

1. The complete source plan, specification, issue, or conversation that the
   tickets are meant to implement.
2. Every ticket, including its acceptance criteria, parent reference, status,
   and blocking relationships.
3. Relevant repository code, ADRs, and project vocabulary when needed to verify
   feasibility or dependency claims.

If either the source requirements or ticket set is unavailable, report the
missing input as a blocker. Do not claim that coverage is complete from ticket
titles alone.

### 2. Audit the ticket set

Check the set as a whole for:

- **Coverage** — every source requirement maps to at least one ticket, and no
  ticket introduces unsupported scope.
- **Verticality** — normal tickets deliver narrow, observable behaviour across
  the required layers instead of grouping work by schema, backend, frontend, or
  tests.
- **Independent verification** — each ticket has a focused way to prove its own
  behaviour before final integration.
- **Agent-sized scope** — each ticket can be understood, implemented, debugged,
  tested, reviewed, and committed within one fresh context window.
- **Dependency correctness** — every blocking edge represents a real prerequisite;
  the graph has no cycles, missing gates, or unnecessary serialization.
- **Parallelism** — tickets with no genuine dependency can proceed independently.
- **Ordering** — enabling prefactors come first, behavioural slices follow, and
  integration or cleanup happens only after its prerequisites are green.
- **Wide-refactor handling** — genuinely mechanical, cross-cutting refactors use
  an expand-migrate-contract sequence rather than a fake vertical slice.

Build a requirement-to-ticket coverage map while reviewing. Use it to identify
missing, duplicated, contradictory, or orphaned work.

### 3. Audit every ticket

For each ticket, verify that:

- the title and `What to build` describe one user-visible or operational outcome;
- acceptance criteria are observable, specific, and jointly describe that outcome;
- the ticket does not mix implementation with final integration proof;
- the `Blocked by` list is complete and minimal;
- its status is justified by the current blocker state;
- it follows the configured tracker format and preserves any required parent;
- it avoids brittle file paths and code snippets unless a decision-rich prototype
  makes one necessary;
- it does not silently absorb unrelated defects or cleanup.

## Reject acceptance epics disguised as tickets

An acceptance objective is not automatically an agent-sized ticket.

Do not confuse:

- one user-facing command with one implementation ticket;
- one end-to-end outcome with one indivisible vertical slice;
- a parent acceptance objective with a `ready-for-agent` tracer bullet;
- final clean-environment proof with the development feedback loop.

Before approving a ticket, verify:

1. It delivers one observable behaviour.
2. It has a focused, agent-runnable red/green verification command.
3. It can be implemented, diagnosed, verified, reviewed, and committed within
   one fresh context.
4. Its acceptance criteria do not contain multiple independently failing
   product boundaries.
5. A late failure does not require replaying unrelated, already-passing phases.
6. Its normal feedback loop does not depend on repeated cold builds,
   destructive resets, production retry windows, or other waits measured in
   tens of minutes.

Reject the ticket as too coarse when:

- different criteria require different debugging seams or runtime profiles;
- the final end-to-end runner is the only way to identify which boundary failed;
- the ticket both implements several gates and proves their final integration;
- the only red/green signal is too expensive to run repeatedly;
- acceptance failures are likely to expand the ticket with unrelated fixes.

Required remediation:

1. Keep the overall outcome as a parent or final integrate-and-verify ticket.
2. Create one tracer-bullet child ticket for each independently verifiable
   behaviour boundary.
3. Give every child ticket a focused verification command and expected runtime.
4. Make the final acceptance ticket depend on all child gates.
5. Limit the final ticket to orchestration, clean-environment execution,
   evidence aggregation, and reporting.
6. Require targeted phase execution or safe retained-state debugging.
7. Preserve one clean full run as final proof, after all child gates are green.
8. When final acceptance reveals an independent defect, create a blocking bug
   ticket instead of silently expanding the acceptance ticket.

Do not approve or publish the ticket set until blocking granularity findings
are resolved.

### Example

Too coarse:

- Build one acceptance command covering authentication, tenant isolation,
  workflow recovery, observability, backup and restore, frontend, and browser
  verification.

Preferred:

- Prove authentication and tenant isolation.
- Prove workflow interruption and recovery.
- Prove operational health and telemetry.
- Prove backup and restore.
- Prove frontend and browser behaviour.
- Integrate the already-green gates into one final acceptance command.

The final integration ticket is blocked by every preceding gate and must not be
the first place those behaviours are implemented or debugged.

## Report

Lead with one verdict:

- **PASS** — the set is complete, correctly ordered, and ready for agents.
- **REVISE** — the set is usable after the listed ticket changes.
- **BLOCKED** — required source material is missing, or the decomposition is too
  unsafe to repair with local edits.

Then report:

1. **Findings**, ordered by severity: `BLOCKER`, `MAJOR`, then `MINOR`.
2. **Coverage gaps**, mapping each uncovered or conflicting requirement to the
   affected ticket or missing ticket.
3. **Dependency corrections**, including edges to add, remove, or reverse.
4. **Recommended ticket changes**, stated as concrete splits, merges, rewrites,
   or reorderings.
5. **What already works**, so sound tickets are not rewritten unnecessarily.

Each finding must name the affected ticket, cite the violated requirement or
ticket-contract rule, explain the implementation risk, and propose the smallest
useful correction. If there are no findings, say so explicitly.
