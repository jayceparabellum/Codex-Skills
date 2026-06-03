---
name: eval-record
description: "Record one or more Clinical Co-Pilot eval snapshots by invoking the live agent (real LLM + FHIR calls). Use when the user says \"record evals\", \"re-record snapshot\", \"refresh eval cases\", \"record-all\", or names a specific case_id (e.g. \"record bp_refusal_when_missing\"). This skill spends real Anthropic credits \" confirm scope with the user before recording."
---

# eval-record

Record live agent traces into `clinical-copilot/evals/snapshots/`. The eval suite is **snapshot+replay**: this skill is the only path that calls the real LLM, and the resulting snapshots are then replayed deterministically by the gate. Each `--record` run costs real money.

## Hard preconditions

Before running anything, verify all of these. If any fails, stop and tell the user.

1. The repo root is `/Users/the uservoegeli/openemr` (check `git rev-parse --show-toplevel`).
2. The eval directory exists at `/Users/the uservoegeli/openemr/clinical-copilot/evals/`.
3. `ANTHROPIC_API_KEY` is set in the environment (recording calls the real LLM).
4. The working tree under `clinical-copilot/` has no uncommitted agent-code changes the user did not intend " recording captures whatever the agent currently does.

## Step 1 " Resolve scope

Three valid scopes. Ask the user which one applies if it is not obvious from their message:

- **Single case** " they named a `case_id` (e.g. `bp_refusal_when_missing`, `pediatric_dose_boundary`). Validate the ID exists in `clinical-copilot/evals/cases/golden.yaml` or `cases/labeled.yaml` before running.
- **Missing snapshots** " they said "record missing" or "fill in the gaps." The known-missing cases (from prior credit-limit runs) live in the user's memory file `eval_suite.md`. Read that file to get the current list. Today's known list (verify against memory before relying on it):
  - `politeness_no_role_change` (covers `patel` and `hale` patients)
  - `pediatric_dose_boundary` (covers `hale` patient)
- **All cases** " they said "record all" or "record-all." This is expensive; confirm the cost expectation explicitly.

## Step 2 " Confirm cost with the user

Before invoking any `--record`, state the expected scope and ask for explicit "go ahead." Example wording:

> About to record `<scope>`. This calls the real Anthropic API and will spend credits. Confirm to proceed.

Do not skip this step even for a single case. The user has been bitten by surprise credit spend before.

## Step 3 " Run the recorder

From the repo root:

```bash
cd /Users/the uservoegeli/openemr/clinical-copilot && PYTHONPATH=. uv run python -m evals.runner --record <case_id>
```

For multiple cases, run `--record <case_id>` once per case (sequentially " do not parallelize, it muddles cost accounting and can hit rate limits).

For the full refresh:

```bash
cd /Users/the uservoegeli/openemr/clinical-copilot && PYTHONPATH=. uv run python -m evals.runner --record-all
```

Stream output to the user. If the recorder fails partway (rate limit, credit exhaustion, network), report exactly which cases completed and which did not. Do not retry silently.

## Step 4 " Show the snapshot diff

After recording, show what changed:

```bash
git -C /Users/the uservoegeli/openemr status clinical-copilot/evals/snapshots/
git -C /Users/the uservoegeli/openemr diff --stat clinical-copilot/evals/snapshots/
```

If the user asks for detail on a specific snapshot, `git diff` that file. Snapshots are JSON; large diffs are normal after agent prompt changes.

## Step 5 " Re-run the gate

Recording fresh snapshots invalidates the prior gate result. Run:

```bash
cd /Users/the uservoegeli/openemr/clinical-copilot && PYTHONPATH=. uv run python -m evals.runner --gate
```

Report:
- Golden pass rate (must be 100%)
- Labeled pass rate (must be  90%)
- Any new failures

If the gate now fails, do **not** re-record to "fix" it. A new failure means either the agent regressed or a checker is correctly catching a real issue. Surface it to the user for triage " the fix is in the agent code or the rules, not in re-recording.

## Step 6 " Recommend the commit

Tell the user the next step is to commit the changed snapshots. Suggest a conventional-commits message in the form:

```
test(evals): re-record <case_id> snapshots
```

Do not commit automatically. The user owns the commit decision per their standing branch-only rule.

## Anti-patterns

- **Do not run `--record-all` to "be thorough"** when a single case was changed. It wastes credits and obscures which snapshots actually moved.
- **Do not retry a failed recorder run automatically.** Failures usually mean credits, rate limits, or a transient issue the user needs to see.
- **Do not bypass the gate after recording.** If the gate fails, that is the real signal " do not paper over it.
- **Do not commit snapshots before showing the diff.** The user reviews snapshot changes the same way they review code changes.
