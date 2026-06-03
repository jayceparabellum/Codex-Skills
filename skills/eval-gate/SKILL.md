---
name: eval-gate
description: "Run the Clinical Co-Pilot eval gate (snapshot replay) and report golden / labeled pass rates plus any failures. Use when the user says \"run the eval gate\", \"check evals\", \"are evals passing\", or before any push that touches agent code. This is read-only \" no LLM calls, no money spent."
---

# eval-gate

Run the deterministic eval gate over the current snapshots and report results clearly. This skill does **not** record snapshots and does **not** call the LLM. It is safe to run any time.

## Hard preconditions

1. The eval directory exists at `/Users/the uservoegeli/openemr/clinical-copilot/evals/`.
2. `uv` is on PATH.

If either fails, stop and tell the user what is missing.

## Step 1 " Run the gate

From the repo root, run:

```bash
cd /Users/the uservoegeli/openemr/clinical-copilot && PYTHONPATH=. uv run python -m evals.runner --gate
```

Capture the full output " the gate prints golden/labeled counts, a failures table (if any), the snapshot path, and the history file path.

## Step 2 " Parse and report

From the output, extract and report exactly these fields:

- **Golden:** `<passed>/<total>` (must be 100% or the gate fails)
- **Labeled:** `<passed>/<total>` (must be  90% or the gate fails)
- **Gate result:** `[gate] OK` or `[gate] FAIL` (look for the literal final line)
- **Failures table:** if present, list each row as `<case - patient> " <rule> " <why>`
- **History file:** the path printed (e.g. `clinical-copilot/evals/history/<timestamp>.json`)

Format the user-facing summary tightly. Three lines is enough for a green run; failures get one line each.

## Step 3 " Compare against the previous run (optional but valuable)

If `clinical-copilot/evals/history/` has more than one file, the second-most-recent file is the prior run. Read it and compare:

- Are there **new** failures that were not in the prior run? Flag these " they indicate a regression introduced since the last gate.
- Are there **resolved** failures that the prior run had? Mention these " the user may want to update tracked follow-ups.

If history has only one file (or none), skip the comparison.

## Step 4 " If the gate fails

Do **not** suggest bypassing. The gate is the source of truth; a failure means either:

- Snapshots are stale (agent code changed since they were recorded). Recommend `$eval-record <case_id>` for the affected cases.
- A rule is correctly catching a real regression. The fix is in the agent code.
- A rule itself is wrong. Less common; user should confirm before changing rule logic.

Surface the failure and the most likely cause based on what the failure row says. Do not guess if the cause is genuinely ambiguous.

## What this skill never does

- Never calls `--record` or `--record-all`. Use `$eval-record` for that.
- Never uploads to LangSmith with `--no-langsmith` flipped " let the runner decide based on env vars.
- Never modifies snapshots, rules, or cases.
- Never recommends `--no-verify` or any other bypass.
