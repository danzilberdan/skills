---
name: lean-iterations
description: Guides projects through .lean/<epic-or-project>/ folders containing a north-star document, sibling state.md, specs/, and small implementation iterations that are always committed and pushed when complete. Use when the user wants an agent to manage iterative product or architecture work, create or maintain a "north star" goal, run looped implementation passes, define iteration-N.md plans, keep progress aligned without micromanaging, or run auto mode where an orchestrator spawns subagents that follow lean-iterations and use probe-me auto-mode.
---

# Lean Iterations

Use this skill to manage long-running project work through a stable north-star document and a sequence of small iteration plans.

The agent should manage the process. The user should not need to decide every next step. Ask the user only for high-leverage decisions, implementation approval, or permission before expensive work.

## Core Structure

By default, create one nested folder per epic or project under `.lean/`. If the user specifies a different directory, use that directory instead.

```text
.lean/
  <epic-or-project-slug>/
    north-star.md
    state.md
    specs/
      <supporting-spec>.md
    iterations/
      iteration-1.md
      iteration-2.md
      ...
```

When the user gives an explicit path, create or maintain the same inner structure at that path:

```text
<explicit-path>/
  north-star.md
  state.md
  specs/
    <supporting-spec>.md
  iterations/
    iteration-1.md
    iteration-2.md
    ...
```

Use the user's requested location if provided. Otherwise choose a short, lowercase, hyphenated folder name under `.lean/` based on the epic or project goal, and mention the chosen path before creating files.

The files have separate roles:

- `north-star.md`: durable product and system architecture target. It may evolve, but should not become a progress journal.
- `state.md`: live operational pointer beside the north star. It says what is complete, what is active, what is next, and what evidence exists.
- `specs/<supporting-spec>.md`: durable product, architecture, research, protocol, or evaluation specs for this epic. Keep them in the epic's `specs/` folder when they are part of the epic's source of truth.
- `iterations/iteration-N.md`: scoped implementation milestone. It records temporary decisions, acceptance criteria, verification, and remaining gaps.

Keep `iterations/` to numbered iteration files by default. Put verification checklists, local runbooks, and iteration-specific evidence inside the relevant `iteration-N.md` unless the user explicitly asks for separate artifacts.
Keep `specs/` for durable supporting specs only, not transient progress notes.

## When To Probe

Use the `probe-me` skill before writing or materially changing the north star when:

- the product outcome is unclear;
- multiple architecture shapes are plausible;
- the target user, success criteria, or non-goals are ambiguous;
- a decision would change several future iterations;
- the user asks for help shaping the plan rather than executing it.

Do not ask questions whose answers are available from project docs, code, or existing iteration files. Inspect first.

Probe one high-leverage question at a time. Give a recommended answer with each question. Stop probing once the next concrete action is clear enough.

In auto mode, use `probe-me` in auto mode: ask the same high-leverage questions internally, choose the answers yourself, and record the assumptions that matter for future correction.

## Operating Modes

At the start of every run, determine whether the user requested regular mode or auto mode. Auto mode is active only when the user explicitly asks for `auto mode`, asks the agent to orchestrate subagents autonomously, or says not to stop for routine decisions.

If auto mode is active, immediately read and follow `auto-mode.md` before planning or implementing anything. In auto mode, the parent agent is an orchestrator, not the primary implementer. A small task is not an exception. The parent may inspect files, decide workflow mode, prepare worker prompts, merge worker outputs, update shared lean files, verify, commit, push, and report. It must not directly perform implementation work before spawning child agents unless subagent tooling is unavailable or blocked.

Then determine the lean workflow mode:

- **Bootstrap:** no north-star file or no `state.md` exists.
- **Refine North Star:** the durable goal is missing, vague, contradicted by later decisions, or no longer matches the user's intent.
- **Plan Next Iteration:** the north star exists, no active unfinished iteration exists, and the full goal is incomplete.
- **Implement Active Iteration:** `state.md` points to an unfinished iteration with clear acceptance criteria.
- **Validate And Close:** implementation appears done but verification/state updates are missing.

Tell the user which run mode and workflow mode you selected and why.

## Auto Mode Orchestration

Auto mode has stricter orchestration requirements than regular mode. Do not rely on this summary alone. Immediately read `auto-mode.md` and follow it exactly whenever auto mode is active.

## Bootstrap Workflow

1. Read relevant project docs and code before asking questions.
2. If the goal is still unclear, use `probe-me`.
3. Choose the lean project directory:
   - use the explicit path if the user provided one;
   - otherwise create `.lean/<epic-or-project-slug>/`.
4. Move or create durable supporting specs for the epic inside the lean project directory's `specs/` folder unless the user explicitly wants them to remain elsewhere.
5. Create `north-star.md` with:
   - product outcome;
   - system architecture target;
   - authoritative references and precedence;
   - accepted decisions;
   - completion checklist;
   - non-goals and non-divergence rules;
   - loop protocol for future agents.
6. Create `state.md`.
7. If the next slice is clear, create `iterations/iteration-1.md`; otherwise use `probe-me` to clarify the first slice.

Ask before creating files if the target structure or naming is uncertain.

In auto mode, choose the target structure yourself unless it would write outside the project or overwrite unrelated existing work. Record the chosen path and reasoning in `state.md`.

## Planning Workflow

When creating the next `iteration-N.md`:

1. Read `north-star.md`, `state.md`, and the latest completed iteration files.
2. Identify the smallest coherent milestone that moves toward the north star.
3. Prefer a vertical slice with visible verification over a broad refactor.
4. Write the iteration plan before implementing.
5. Ask the user before implementation unless they explicitly asked the agent to proceed autonomously.

In auto mode, treat the user as having approved routine planning and implementation within the active lean scope. Still keep iterations small enough that mistakes are easy to reverse.

Each iteration file should include:

```markdown
# Iteration N: <short title>

## Current Checkpoint

## Goal

## Decisions

## Implementation Work

## Out Of Scope For This Iteration

## Acceptance Criteria

## Verification

## Remaining Gaps Toward The North Star
```

Out-of-scope items are only out of scope for that iteration. They are not removed from the north star unless the north star is deliberately changed.

## Implementation Workflow

When implementing an active iteration:

1. Re-read `north-star.md`, `state.md`, and the active iteration.
2. Confirm the intended scope to the user before substantial edits or expensive commands, except in auto mode where the agent records the scope decision and proceeds when the work is routine and reversible.
3. Implement only the active iteration unless the user approves expanding scope.
4. Keep unrelated user changes intact.
5. Test or validate according to the iteration plan.
6. If the plan is wrong, update the iteration plan before continuing.
7. Do not mark the iteration complete until acceptance criteria, verification evidence, commit hash, and push evidence are recorded.

Ask before heavy tasks such as long-running services, migrations, large refactors, expensive test suites, external API usage, destructive operations, or broad generated changes. In auto mode, ask only for destructive, externally costly, secret-dependent, or high-consequence irreversible work; otherwise choose the conservative path and document the assumption.

## Commit And Push Requirement

After completing an iteration, always commit and push the completed work in both regular mode and auto mode.

Completion requires:

1. Acceptance criteria are satisfied.
2. Verification evidence is recorded in the iteration file or `state.md`.
3. Relevant changes are committed with a message that names the iteration.
4. The commit is pushed to the active branch's remote.
5. `state.md` records the commit hash and pushed branch.

Before committing, inspect the working tree and include only changes that belong to the completed iteration. Never stage unrelated user changes. If push is blocked by missing credentials, network failure, branch protection, or absent remote configuration, record the blocker in `state.md` and do not mark the iteration complete.

## State File Contract

`state.md` should be compact and operational. Keep it current after every meaningful loop.

Recommended shape:

```markdown
# Iteration State

North star: north-star.md
Overall status: incomplete
Latest completed iteration: none
Active iteration: iteration-1.md

## Current Checkpoint

## Verification Evidence

## Commit And Push Evidence

## Next Known Gap

## Loop Instructions
```

Use `Overall status: complete` only when the north-star completion checklist is satisfied.

## North-Star Update Rules

Update the north star only for durable changes:

- product goal changes;
- architecture target changes;
- accepted decision supersedes an old decision;
- completion checklist changes;
- non-goal or boundary changes.

Do not update the north star for ordinary progress. Put progress in `state.md` and iteration files.

When changing the north star, explain the change to the user and ask first unless the user explicitly asked for autonomous planning updates.

## Non-Divergence Rules

- Always anchor work to the north star before using iteration docs.
- Keep the epic's durable source-of-truth docs inside the same lean project folder by default, not scattered across unrelated docs folders.
- Never let the latest iteration become the full goal by accident.
- Never treat an iteration's temporary out-of-scope list as permanently out of scope.
- Do not create sidecar docs in `iterations/` for verification or progress by default; fold them into the relevant numbered iteration file.
- Prefer explicit acceptance criteria over vague "continue improving" work.
- Prefer small, validated milestones over large speculative rewrites.
- Preserve decision history. If a later decision supersedes an earlier one, say so instead of deleting the trail.
- Ask the user before implementation or heavy work when the cost or direction is not obvious.

## Example User Invocation

```text
Use the lean-iterations skill for a new billing-reconciliation epic. Manage the north star and iterations under .lean/ by default. Probe me when the durable goal or next slice is unclear, then ask before implementation.
```

```text
Use the lean-iterations skill for docs/my-project-plan. Since I specified a path, manage the north star and iterations there instead of .lean/.
```

```text
Use lean-iterations in auto mode for the search-relevance epic. Act as the orchestrator, spawn subagents for independent planning, implementation, and validation work, and have every subagent use probe-me in auto mode when decisions are unclear.
```
