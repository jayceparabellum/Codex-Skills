---
name: lesson-capture
description: Capture reusable lessons after meaningful work, failures, debugging sessions, project milestones, prompt issues, or process improvements. Use when Codex should extract what worked, what failed, what to repeat, what to avoid, and whether a new rule, skill, checklist, or memory should be created.
---

# Lesson Capture

Use this skill to turn experience into reusable process. The goal is to make the next similar task easier and less error-prone.

## Operating Rules

- Capture lessons only when there is a real signal: failure, repeated friction, surprising success, recurring user preference, or a meaningful milestone.
- Keep lessons actionable.
- Separate facts from interpretations.
- Do not create permanent rules from one weak data point.
- Suggest a skill or global rule only when the pattern is likely to recur.

## Lesson Shape

```markdown
What happened:

What worked:

What failed or slowed us down:

Root pattern:

Rule to keep:

Skill / checklist opportunity:

Where to save it:
```

## Workflow

1. Summarize the event in one or two sentences.
2. Identify the reusable pattern.
3. Extract one rule that would have improved the outcome.
4. Decide whether the lesson belongs in:
   - final response only
   - project docs
   - global `AGENTS.md`
   - a new or existing skill
   - a repo checklist
5. Ask before writing persistent memory or global rules unless the user already requested it.

## Good Lessons

Good:

```text
Before converting external skills, normalize frontmatter and encoding before validation.
```

Weak:

```text
Be more careful.
```

Good:

```text
When deleting a skill, search dependent skills for references to the removed toggle.
```

Weak:

```text
Deletion is risky.
```

The lesson should change future behavior.
