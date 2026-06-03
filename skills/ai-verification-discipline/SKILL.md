---
name: ai-verification-discipline
description: Build and execute an evidence checklist before declaring AI engineering work complete. Use when Codex needs to verify implementation quality with tests, local runs, browser checks, API checks, logs, screenshots, documentation evidence, or final submission proof.
---

# AI Verification Discipline

Use this skill to prevent "looks done" from being mistaken for "is done." Completion requires evidence that matches the risk of the change.

## Operating Rules

- Verify from the user's perspective whenever the work is user-facing.
- Run the narrowest meaningful checks first, then broaden when the change touches shared behavior.
- Prefer reproducible commands and captured outputs over verbal confidence.
- If a check cannot be run, say why and name the remaining risk.
- Do not claim completion when only implementation was performed.

## Verification Ladder

Choose the highest relevant rungs:

1. Static checks: lint, typecheck, formatting, schema validation.
2. Unit checks: targeted tests for changed logic.
3. Integration checks: API, database, auth, service boundaries.
4. Runtime checks: start the app, exercise the workflow, inspect logs.
5. Browser or UI checks: screenshots, console errors, network failures, responsive layouts.
6. Edge checks: empty states, invalid input, offline/slow network, permissions, retries.
7. Release checks: build, deploy, migration, rollback path, final documentation.

## Evidence Checklist

Before finalizing, capture:

```markdown
What changed:

Checks run:

Result:

Evidence:

Known gaps:

Residual risk:
```

## Failure Handling

When a check fails:

1. Preserve the failure output.
2. Identify the smallest failing surface.
3. Form one hypothesis at a time.
4. Change only what the evidence supports.
5. Re-run the failing check.
6. Run a nearby regression check before finalizing.

## Done Criteria

Work is done when:

- The requested behavior is implemented.
- The acceptance checks pass or are explicitly blocked.
- User-facing behavior has been exercised when applicable.
- The final response includes the checks that actually ran.
- Any unverified area is stated plainly.

If the evidence is weak, call the work "implemented, not fully verified" and recommend the next check.
