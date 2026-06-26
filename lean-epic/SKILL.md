---
name: lean-epic
description: Manage long-running project work through a .lean epic folder with a durable north-star.md, operational state.md, supporting specs, and small iteration plans. Use when the user wants an agent to shape an epic, maintain the north star, plan or implement the next iteration, keep progress aligned, and commit/push completed slices.
---

# Lean Epic

Use this skill for epic-sized work that needs a durable goal and a sequence of small, verified implementation slices.

## Structure

Default to `.lean/<epic-slug>/` unless the user gives another path.

```text
.lean/<epic-slug>/
  north-star.md
  state.md
  specs/
  iterations/
    iteration-1.md
```

- `north-star.md`: durable product goal, architecture direction, accepted decisions, non-goals, and completion checklist.
- `state.md`: current status, active iteration, verification evidence, commit/push evidence, and next gap.
- `specs/`: durable supporting specs for the epic.
- `iterations/iteration-N.md`: one small milestone expressed only as required, specific outcomes.

Keep progress out of the north star. Put progress in `state.md` and iteration files.

## Epic Selection

Never assume which lean epic the user means. If the user does not explicitly give the epic path, epic slug, or a node-specific instruction that uniquely identifies the epic, validate the target before reading or writing epic docs.

Validation means inspect available candidates, tell the user which epic you think they mean, and get confirmation before proceeding. Do not choose an epic based only on recency, current branch, folder names, or there being a single obvious-looking `.lean/<epic-slug>/` folder.

## Startup

1. Validate the target epic unless the user explicitly gave it.
2. Read existing lean epic files and relevant project context before asking questions.
3. Use `probe-me` when the product outcome, architecture, success criteria, non-goals, or next slice is unclear.
4. Choose the workflow mode:
   - **Bootstrap:** create missing `north-star.md` or `state.md`.
   - **Refine North Star:** update durable goals or decisions.
   - **Plan Next Iteration:** write the next `iteration-N.md`.
   - **Implement Active Iteration:** execute the iteration named in `state.md`.
   - **Validate And Close:** verify, update state, commit, and push.
5. Tell the user the selected mode and why.

Ask only for high-leverage decisions, implementation approval, or costly/risky work. Do not ask questions answerable from the repo or existing docs.

## Bootstrap

Create the lean epic folder, then write:

- `north-star.md` with outcome, architecture target, references, accepted decisions, completion checklist, non-goals, and loop instructions.
- `state.md` with overall status, active iteration, checkpoint, verification evidence, commit/push evidence, next gap, and loop instructions.
- `iterations/iteration-1.md` if the first slice is clear.

Ask before creating files when the target path or naming is uncertain.

## Plan Iterations

Each iteration should be the smallest coherent milestone that moves toward the north star, preferably a vertical slice with visible verification.

Iteration files must be concise. Write only a list of required outcomes. Do not include background, rationale, optional work, stretch goals, nice-to-haves, alternatives, open questions, or prose explaining how to use the plan.

Every outcome must be 100% specific: name the exact artifact, behavior, command, test, document, commit, or evidence required. Avoid vague categories such as "improve", "handle", "support", "as needed", "if possible", "consider", "may", "could", "optional", "ideally", or "where appropriate".

There is no optional language in an iteration file under any circumstance. If an item is not required for the iteration to close, omit it. If the item is required only under a condition, either make the condition exact and testable or split it into a later iteration.

Use this shape:

```markdown
# Iteration N: <short title>

- <required outcome 1>
- <required outcome 2>
- <required outcome 3>
```

## Implement

1. Re-read `north-star.md`, `state.md`, and the active iteration.
2. Confirm scope before substantial edits or expensive commands.
3. Implement only the active iteration unless the user expands scope.
4. Preserve unrelated user changes.
5. Run the planned verification, or record why it could not run.
6. Update the iteration plan if reality invalidates it.

Do not mark an iteration complete until acceptance criteria, verification evidence, commit hash, and push evidence are recorded.

## Guardrails

- Anchor work to the north star before using iteration docs.
- Keep durable epic docs inside the lean epic folder by default.
- Keep iterations small, explicit, and verifiable.
- Preserve decision history; supersede old decisions instead of erasing the trail.
- Ask before destructive operations, migrations, broad generated rewrites, external costs, secret use, or high-consequence irreversible work.

## Examples

```text
Use lean-epic for a new billing-reconciliation epic under .lean/.
```

```text
Use lean-epic for docs/my-project-plan and manage the north star and iterations there.
```
