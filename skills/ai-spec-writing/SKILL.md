---
name: ai-spec-writing
description: Turn fuzzy AI engineering requests into crisp, testable specifications before implementation. Use when Codex needs to clarify a product or engineering task, convert an assignment into requirements, define done criteria, plan milestones, prevent scope drift, or prepare work for agent execution.
---

# AI Spec Writing

Use this skill to turn ambiguous work into a small executable spec. Keep the spec short enough to guide action, but concrete enough that another engineer or agent could verify completion.

## Operating Rules

- Start from the user's goal, not from the apparent implementation.
- Prefer a written spec before code when the request affects product behavior, architecture, data, deployment, user workflows, or grading/submission criteria.
- Ask at most three clarifying questions only when the missing information would change the implementation materially.
- If the user wants speed, write a lightweight spec inline and proceed.
- Treat the spec as a contract: update it when reality changes.

## Spec Shape

Produce these sections when useful:

```markdown
Goal:

Users / stakeholders:

Current state:

Desired behavior:

Non-goals:

Constraints:

Acceptance checks:

Implementation slices:

Risks / unknowns:

Evidence to collect:
```

## Execution Workflow

1. Restate the core outcome in one sentence.
2. Identify who benefits and what workflow changes.
3. Separate requirements from guesses.
4. Define acceptance checks as observable facts, commands, screenshots, tests, API responses, or deploy URLs.
5. Split implementation into the smallest useful slices.
6. Name risks early: auth, data loss, migrations, performance, cost, security, deadlines, or grading requirements.
7. Confirm or proceed with the smallest reasonable assumption.

## Quality Bar

A good spec makes these questions easy to answer:

- What exactly will change?
- What will not change?
- How will we know it works?
- What can be implemented first without blocking later work?
- What evidence should appear in the final report?

## Agent Handoff

When preparing work for another agent, include:

- Objective
- Relevant files or systems
- Constraints
- Allowed and disallowed changes
- Required verification
- Expected final response format

Do not hand off a vague instruction like "fix this" when the issue can be framed as a measurable outcome.
