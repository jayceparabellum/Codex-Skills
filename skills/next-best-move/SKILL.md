---
name: next-best-move
description: Rank possible next actions by deadline, impact, dependency, risk, and effort so Codex chooses the highest-leverage next step. Use when the user asks what to do next, feels scattered, has multiple project paths, needs prioritization, or wants a decision about the next assignment or engineering move.
---

# Next Best Move

Use this skill to choose the next action when there are multiple plausible paths. The goal is not a perfect roadmap; it is a clear next move with a reason.

## Ranking Criteria

Score informally using:

- Deadline: time sensitivity or external due date.
- Impact: user, grade, revenue, reliability, or learning value.
- Dependency: whether other work is blocked by this.
- Risk: chance of failure, data loss, merge conflict, deploy issue, or wasted effort.
- Effort: time and complexity required to get a useful result.
- Evidence: whether enough facts exist to act responsibly.

## Output Shape

```markdown
Recommended next move:

Why this first:

Do now:

Defer:

Evidence needed:

Stop / reassess when:
```

## Workflow

1. List the real candidate moves.
2. Remove anything blocked by missing facts unless gathering the facts is the next move.
3. Prefer moves that unblock other work.
4. Prefer externally required deliverables over optional polish.
5. Prefer verification before expansion.
6. State the next concrete command, file, check, or decision.

## Tie Breakers

When two moves look close:

- Choose the one that reduces uncertainty fastest.
- Choose the one with the clearest completion evidence.
- Choose the one that protects a deadline.
- Choose the one that makes future AI-agent work easier.

Avoid vague endings like "continue improving." Name the next action.
