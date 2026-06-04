---
name: preflight
description: Run a short pre-work gate before coding, editing files, pushing, deploying, or using agents. Use when Codex needs to check the goal, assumptions, repo state, risky operations, verification plan, rollback path, or whether user confirmation is needed before acting.
---

# Preflight

Use this skill before meaningful action. It should be fast: enough to prevent avoidable mistakes, not enough to stall the work.

## Operating Rules

- Run preflight before file edits, migrations, deploys, pushes, broad refactors, or agent delegation.
- Keep it brief for low-risk work.
- Escalate only when the operation is destructive, irreversible, security-sensitive, costly, or ambiguous.
- Never hide a risky assumption.

## Preflight Checklist

```markdown
Goal:

Workspace / repo:

Current state:

Assumptions:

Risky operations:

Files or systems likely touched:

Verification plan:

Rollback / recovery:

Proceeding with:
```

## Workflow

1. Confirm the objective in one sentence.
2. Inspect relevant local state when available: repo status, file presence, current branch, config, running services, or prior artifacts.
3. Identify assumptions that could change the outcome.
4. Identify risky operations: delete, move, migration, force push, secret exposure, production changes, bulk edits, dependency upgrades.
5. Define the minimum verification plan before editing.
6. Proceed, ask, or stop.

## Stop Conditions

Stop and ask when:

- The target repo or environment is unclear.
- A destructive operation is needed.
- Production data or secrets may be affected.
- The request conflicts with known project rules.
- Verification cannot be performed and the risk is high.

For ordinary low-risk edits, state the assumption and continue.
