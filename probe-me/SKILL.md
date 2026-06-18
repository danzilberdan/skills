---
name: probe-me
description: Use when the user wants to be probed on a plan, design, strategy, or decision. Ask high-leverage questions that most reduce the possibility space, compare viable approaches, and iteratively converge on one clearly recommended path. Only use look-aheads for implicit predictions the agent is making from likelihood and intuition, not for assumptions or constraints already stated by the user.
---

# Probe Me

Help the user converge on the best approach with the fewest high-value questions.

## Workflow

1. Identify the main decision or design space.
2. Briefly infer the plausible approaches.
3. Ask one high-leverage question at a time.
4. For each question, explain why this question matters and give your current recommended answer.
5. After each user answer, update the possibility space and ask the next most differentiating question.
6. When one approach is clearly better, propose it as a **look ahead** only for predictions that are still implicit and agent-inferred.
7. Invite the user to correct assumptions, add missing context, or reject the look ahead.
8. Continue iteratively until the result is satisfying to the user.

## Look Ahead

Use a look ahead when the answers so far point toward a likely recommendation, even if some uncertainty remains.

A look ahead is only for implicit predictions the agent is making from likelihood, pattern matching, and intuition. Do not use it to restate assumptions, constraints, preferences, facts, decisions, or requirements that the user has already explicitly given or defined in the conversation.

Before offering a look ahead, separate:

- **Explicit context**: things the user has already said or defined. Treat these as inputs, not predictions.
- **Implicit predictions**: likely future implications, unstated preferences, likely constraints, or likely best-fit approaches inferred by the agent.

Only put implicit predictions in the look ahead. If there are no meaningful implicit predictions, ask the next high-leverage question instead.

If the user corrects or adds context, return to the main workflow.

Format it as:

```markdown
**Look Ahead**
Based on what I know so far, I would recommend <approach> because <reason>.

Decision details I am taking as likely:
- <implicit prediction>: <agent's current judgment and why it seems likely>
- <implicit prediction>: <agent's current judgment and why it seems likely>

The main alternatives are <alternative A> and <alternative B>, but they look worse because <tradeoff>.

Do you approve of this look ahead?
```

Keep the look ahead provisional. The user can correct, comment, or add context. In that case, update your mental model and return to the main workflow.

## Question Selection

Prefer questions that:

- Split several plausible approaches apart.
- Resolve a decision that many later decisions depend on.
- Distinguish between "must have", "nice to have", and "actively avoid".
- Reveal whether a simple approach is good enough.

Avoid questions that:

- Explore implementation details before the strategic shape is clear.
- Ask for information available by inspecting the codebase or documents.
- Depend on answers to earlier unresolved questions.
- Would not change the recommendation.

If a question can be answered by exploring the codebase, docs, or artifacts, inspect them instead of asking the user.
