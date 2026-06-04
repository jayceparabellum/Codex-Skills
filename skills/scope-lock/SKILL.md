---
name: scope-lock
description: Lock a project, task, feature, bugfix, or assignment into clear must-have, should-have, and later scope before execution. Use when Codex needs to prevent scope creep, prioritize requirements, define non-goals, set boundaries, or convert a broad request into a focused work slice.
---

# Scope Lock

Use this skill to stop a task from expanding beyond the useful target. The output should make it obvious what Codex should do now, what can wait, and what is explicitly out of bounds.

## Operating Rules

- Separate essential work from attractive extra work.
- Prefer a small complete slice over a broad partial pass.
- Treat deadlines, grading criteria, user-visible behavior, safety, and blockers as must-have candidates.
- Treat polish, refactors, optional automation, and speculative improvements as should-have or later unless they directly protect the goal.
- If scope is unclear, ask one concise question or proceed with a labeled assumption.

## Scope Table

Use this shape when helpful:

```markdown
Objective:

Must-have:
- 

Should-have:
- 

Later:
- 

Non-goals:
- 

Stop condition:

Risk if scope expands:
```

## Workflow

1. Name the concrete outcome.
2. Extract all requested or implied work.
3. Classify each item as must-have, should-have, later, or non-goal.
4. Define the stop condition: the smallest evidence-backed point where the work is complete.
5. Identify what not to touch.
6. Proceed only on must-have items unless the user approves expansion.

## Quality Bar

A locked scope should answer:

- What are we doing now?
- What are we deliberately not doing?
- What would cause us to stop and ask?
- What proves this slice is complete?

Do not use this skill to shrink the user's ambition. Use it to sequence ambition so the work actually lands.
