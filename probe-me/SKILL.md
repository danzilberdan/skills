---
name: probe-me
description: Use when the user wants to be probed on a plan, design, strategy, or decision, including auto-mode probing where the agent asks the same high-leverage questions internally and chooses the answers itself. Ask high-leverage questions that most reduce the possibility space, compare viable approaches, and iteratively converge on one clearly recommended path while letting the user correct or redirect the look-ahead when not in auto mode.
---

# Probe Me

Help the user converge on the best approach with the fewest high-value questions.

Unlike a full grilling session, do not walk every branch of the decision tree. Search for the questions whose answers would most differentiate the viable approaches, eliminate the most uncertainty, or expose the biggest hidden constraint.

## Workflow

1. Identify the main decision or design space.
2. Briefly infer the plausible approaches.
3. Ask one high-leverage question at a time.
4. For each question, explain why this question matters and give your current recommended answer.
5. After each user answer, update the possibility space and ask the next most differentiating question.
6. When one approach is clearly better, propose it as a **look ahead**.
7. Invite the user to correct assumptions, add missing context, or reject the look ahead.
8. Continue iteratively until the result is satisfying to the user.

## Auto Mode

Use auto mode when the user explicitly asks for `probe-me` in auto mode, asks an agent to decide without further questions, or another skill delegates to `probe-me` with auto mode enabled.

In auto mode, run the same probing loop internally:

1. Identify the main decision or design space.
2. Inspect available artifacts before inventing answers.
3. Ask the next high-leverage question internally.
4. Choose the answer yourself using evidence, project context, reversibility, and the user's stated goals.
5. Record the assumed answer briefly instead of asking the user.
6. Continue until one approach is clear enough for action.
7. Output the recommendation plus the assumptions that would be most useful for the user to correct later.

Do not stop to ask the user in auto mode unless continuing would require secrets, external access the agent does not have, destructive action, or a high-consequence irreversible decision that cannot be made conservatively.

For each important internal question, use this compact form:

```markdown
- Question: <high-leverage question>
  Assumed answer: <chosen answer>
  Why: <evidence or judgment>
  Confidence: <high|medium|low>
```

Prefer reversible, small-scope answers when evidence is thin. If two options are close, pick the one that keeps more future options open and mark it as a low-confidence assumption.

## Question Selection

Prefer questions that:

- Split several plausible approaches apart.
- Resolve a decision that many later decisions depend on.
- Surface constraints around users, scope, risk, time, budget, ownership, compatibility, or reversibility.
- Distinguish between "must have", "nice to have", and "actively avoid".
- Reveal whether a simple approach is good enough.

Avoid questions that:

- Explore implementation details before the strategic shape is clear.
- Ask for information available by inspecting the codebase or documents.
- Depend on answers to earlier unresolved questions.
- Would not change the recommendation.

If a question can be answered by exploring the codebase, project docs, or available artifacts, inspect them instead of asking the user.

## Look Ahead

Use a look ahead when the answers so far point toward a likely recommendation, even if some uncertainty remains.

Format it as:

```markdown
**Look Ahead**
Based on what I know so far, I would recommend <approach> because <reason>.

Decision details I am taking as likely, even though they are still uncertain:
- <detail or assumption>: <agent's current judgment and why>
- <detail or assumption>: <agent's current judgment and why>

The main alternatives are <alternative A> and <alternative B>, but they look worse because <tradeoff>. The next question that could change this is: <question>.
```

Keep the look ahead provisional. Include uncertain-but-actionable decision details when they help the user react to the shape of the recommendation. The user can correct, comment, or add context, and you should update the recommendation accordingly.

In auto mode, include the same look ahead, but phrase it as the selected working approach. Add a short `Auto-mode assumptions` list containing only the assumptions most likely to affect future work.

## Operating Rules

- Ask questions one at a time.
- Stay focused on reducing uncertainty, not collecting every detail.
- State your current recommendation as soon as there is enough signal.
- Keep a compact running model of the decision space: leading option, rejected options, unresolved constraints.
- Stop probing once the recommendation is clear enough for action or the user says the result is satisfying.
- In auto mode, never ask the user routine preference questions. Decide, document the assumption, and keep moving.
