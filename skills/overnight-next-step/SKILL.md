---
name: overnight-next-step
description: Run a guarded overnight continuation loop for an existing project by repeatedly applying `$ce-work` to the next pending roadmap/task item. Use when the user asks Codex to keep implementing the next step, repeat the prompt "[$ce-work] let's implement the next step", continue work overnight, or run a safe autonomous next-step sequence without investor polish, live trading, force pushes, or broad scope changes.
---

# Overnight Next Step

## Purpose

Repeat the user's approved next-step implementation prompt safely:

```text
[$ce-work](C:\Users\jayce\.codex\skills\ce-work\SKILL.md) let's implement the next step
```

Use the active project's own roadmap, task tracker, `AGENTS.md`, and local skill
instructions as the source of truth. Keep each cycle small, verifiable, and
auditable.

## Operating Rules

- Use `$ce-work` for each implementation cycle.
- Use `$preflight` before edits, pushes, risky commands, or agent delegation.
- Use the project's required verification skill before calling a step complete.
- Prefer the next pending task in the local task tracker or roadmap.
- Respect the active repo's stated mission, non-goals, safety rules, and branch.
- Keep work local-first and reviewable.
- Do not read credentials or secret files unless the current user explicitly
  approves that exact access.
- Do not enable live trading, production writes, destructive commands, force
  pushes, dependency upgrades, or broad refactors unless the current user
  explicitly asks.
- Do not commit, push, open a PR, or create an automation unless the current
  user explicitly asks in the active thread.

## Cycle

For each overnight invocation:

1. Inspect the active workspace state:
   - current branch and dirty files
   - latest task tracker or roadmap
   - project instructions such as `AGENTS.md`
   - prior uncommitted work
2. Identify exactly one next pending task.
3. Run a brief preflight:
   - goal
   - assumptions
   - likely files touched
   - risky operations avoided
   - verification plan
   - rollback/recovery path
4. Implement the smallest complete vertical slice for that task.
5. Add or update focused tests for behavior-bearing changes.
6. Run verification:
   - focused tests for changed behavior
   - full local test suite when practical
   - task-specific quality gates
   - JSON/schema/format checks for edited control files
   - secret scan over changed surfaces when credentials or audit payloads are in scope
7. Mark the task complete only when acceptance criteria are actually met.
8. Stop and report if blocked, tests fail, the task is ambiguous, or the next
   action would require user approval.

## Overnight Stop Conditions

Stop the loop instead of forcing progress when:

- the next task needs a credential, API key, paid service, live account, or
  production state that is not already approved for this exact run
- tests or verification fail and cannot be fixed locally with bounded changes
- the next action is commit/push/PR/deploy/scheduler setup without explicit user
  approval
- the roadmap is exhausted or the next pending task is unclear
- implementation would blend unrelated project traits into this project
- the repo has conflicting user changes that make the task unsafe to continue

## Reporting

At the end of each cycle, report:

- task completed or blocker reached
- important files changed
- verification commands and outcomes
- whether a commit/push was intentionally not performed
- the next pending task, if obvious

Keep the report concise enough for morning review.
