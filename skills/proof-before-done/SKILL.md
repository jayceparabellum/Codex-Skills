---
name: proof-before-done
description: Require concrete evidence before Codex says work is done. Use at the end of implementation, debugging, setup, deployment, refactoring, document generation, or assignment work to list checks run, artifacts created, outputs inspected, gaps, and remaining risk.
---

# Proof Before Done

Use this skill as the final gate between "I changed something" and "this is complete." It is stricter than a summary and lighter than a full audit.

## Operating Rules

- Do not claim "done" without evidence.
- Prefer actual command results, file paths, screenshots, logs, URLs, tests, or validation output.
- Match proof depth to risk.
- If proof is incomplete, say "implemented but not fully verified."
- Include failed checks if they happened.

## Proof Packet

```markdown
Outcome:

Evidence:
- 

Checks run:
- 

Artifacts:
- 

Not verified:
- 

Remaining risk:

Done status:
```

## Evidence Examples

- Test command and pass/fail result.
- Build command and result.
- Local app URL exercised.
- Screenshot path.
- API response shape or status.
- Git commit hash.
- Created file path.
- Deployment URL and health check.
- Validator output.

## Workflow

1. Compare the user's request to what was actually changed.
2. Gather proof already produced during the turn.
3. Run missing high-value checks when feasible.
4. Name artifacts and exact paths.
5. State unverified areas plainly.
6. Give a completion status:
   - `Done`
   - `Implemented, not fully verified`
   - `Blocked`
   - `Needs follow-up`

The final answer should make it easy for the user to trust or challenge the result.
