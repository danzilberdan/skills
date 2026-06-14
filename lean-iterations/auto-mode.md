# Lean Iterations Auto Mode

Read this file whenever `lean-iterations` is running in auto mode.

Auto mode is active only when the user explicitly asks for `auto mode`, asks the agent to orchestrate subagents autonomously, or says not to stop for routine decisions.

## Prime Directive

In auto mode, the parent agent is an orchestrator, not the primary implementer.

The parent agent must spawn child agents before implementation work unless subagent tooling is unavailable or blocked. A small task is not an exception.

The parent agent may:

- inspect the lean project files and relevant repository context;
- decide workflow mode;
- prepare bounded worker assignments;
- merge worker outputs;
- update shared lean files;
- run or coordinate verification;
- commit and push completed iteration work;
- report the final state and assumptions.

The parent agent must not directly perform implementation work before spawning child agents, except in the explicit serial-mode escape hatch below.

## Required Startup Checklist

Before any implementation in auto mode, confirm all of these:

- [ ] Read `north-star.md`, if it exists.
- [ ] Read `state.md`, if it exists.
- [ ] Read relevant `specs/` files and latest iteration files.
- [ ] Declared run mode: `auto mode`.
- [ ] Declared lean workflow mode: Bootstrap, Refine North Star, Plan Next Iteration, Implement Active Iteration, or Validate And Close.
- [ ] Launched at least one child agent, unless subagent tooling is unavailable or blocked.
- [ ] Worker assignments have clear, non-overlapping file ownership or are explicitly read-only.
- [ ] The orchestrator owns shared coordination files unless updates are deliberately serialized.

If any item is not satisfied, complete it before implementation.

## Workflow Order

Follow this order exactly:

1. Read the lean project files and relevant repository context.
2. Decide run mode and lean workflow mode.
3. If no active iteration exists, spawn planning or research workers before writing the next iteration plan.
4. Merge worker outputs into the next `iterations/iteration-N.md` plan.
5. Spawn implementation worker(s) for non-overlapping scopes.
6. Spawn validation/review worker(s), or run verification directly when the validation is a simple local command.
7. Merge worker outputs into source changes and coordination docs.
8. Resolve conflicts conservatively, preserving decision history and recording rejected alternatives.
9. Update `state.md` with checkpoint, verification evidence, commit/push status, and next known gap.
10. Commit and push completed iteration work.
11. Record commit hash and pushed branch in `state.md`.
12. If recording push evidence creates another change, commit and push that evidence too.
13. Report final state, evidence, pushed commit(s), and important auto-mode assumptions.

## Worker Rules

Each worker must receive:

- lean project path;
- run mode and workflow mode;
- active iteration or planning target;
- relevant constraints from `north-star.md`, `state.md`, specs, and latest iterations;
- explicit file ownership or read-only scope;
- acceptance criteria;
- expected verification;
- instruction to use `probe-me` in auto mode whenever it would otherwise probe.

Workers may edit only within assigned scope. Workers must not edit shared coordination files (`north-star.md`, `state.md`, or the same `iteration-N.md`) unless the orchestrator assigns that file exclusively to one worker or serializes the update.

Prefer parallel workers for independent research, planning alternatives, validation, or implementation slices that touch separate files. Use read-only workers for discovery and validation when write conflicts are likely.

## Serial-Mode Escape Hatch

Serial auto mode is allowed only when subagent tooling is unavailable or blocked.

Do not use serial mode merely because the task seems small.

When using serial mode:

1. Say that subagent tooling is unavailable or blocked.
2. Record the reason in `state.md`.
3. Continue as a single-agent orchestrator, preserving the same workflow order as much as possible.

## Safety Gates

Auto mode does not remove safety gates.

Ask before:

- destructive operations;
- spending external money;
- exposing secrets;
- large migrations;
- high-consequence irreversible changes;
- broad generated rewrites;
- expensive or long-running commands whose cost/risk is not already accepted by the user.

Routine planning, implementation, focused tests, ordinary commits, and pushes within the active lean scope are pre-approved in auto mode.

## Planning Worker Prompt Template

```text
Use lean-iterations in auto mode as a read-only planning worker.

Lean project path: <path>
Workflow mode: <mode>

Read:
- <path>/north-star.md
- <path>/state.md
- relevant specs under <path>/specs/
- latest completed iterations under <path>/iterations/
- relevant repository files needed to understand current implementation

Task:
Recommend the next smallest coherent iteration toward the north star.

Return:
- recommended iteration title
- goal
- key decisions and assumptions
- implementation work
- out-of-scope items for this iteration only
- acceptance criteria
- verification plan
- likely touched files or ownership boundaries
- risks and rejected alternatives

Do not edit files.
Use probe-me in auto mode if a high-leverage decision is unclear.
```

## Implementation Worker Prompt Template

```text
Use lean-iterations in auto mode as an implementation worker.

Lean project path: <path>
Active iteration: <path>/iterations/iteration-N.md
Assigned scope: <files/directories>
Do not edit: north-star.md, state.md, or files outside assigned scope.

Read:
- <path>/north-star.md
- <path>/state.md
- <path>/iterations/iteration-N.md
- relevant specs under <path>/specs/
- assigned source files

Task:
Implement only the assigned portion of the active iteration.

Acceptance criteria:
- <criteria>

Verification:
- <commands/checks>

Return:
- changes made
- verification run and results
- any blockers
- remaining gaps

Use probe-me in auto mode if a high-leverage decision is unclear.
```

## Validation Worker Prompt Template

```text
Use lean-iterations in auto mode as a validation worker.

Lean project path: <path>
Active iteration: <path>/iterations/iteration-N.md
Scope to validate: <files/directories/commands>

Read:
- <path>/north-star.md
- <path>/state.md
- <path>/iterations/iteration-N.md
- changed files in scope

Task:
Validate whether the implementation satisfies the active iteration acceptance criteria.

Return:
- pass/fail by acceptance criterion
- verification commands run or why they were not run
- bugs, risks, or missing evidence
- recommended fixes, if any

Do not edit files unless explicitly assigned.
```
