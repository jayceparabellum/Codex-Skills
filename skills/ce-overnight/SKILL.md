---
name: ce-overnight
description: Run a safe overnight Compound Engineering cycle that combines ce-compound, ce-plan, and ce-work. Use when the user wants Codex to prepare or run long unattended work overnight, wake up later with progress, compound prior learning before execution, produce a durable plan, execute bounded work, checkpoint, verify, and stop safely instead of drifting.
---

# CE Overnight

Use this skill for overnight or unattended engineering runs. It is inspired by the retired `dream` idea, but it does not revive the retired dream engine. It composes active skills:

- `$ce-compound` for knowledge compounding and solution capture
- `$ce-plan` for durable planning
- `$ce-work` for bounded execution
- `$scope-lock`, `$preflight`, and `$proof-before-done` as safety gates

## Operating Rules

- Do not run open-ended work without a scope lock.
- Do not start overnight execution on a dirty or ambiguous repo without recording the state.
- Do not bypass hooks, tests, CI gates, force pushes, or safety mechanisms.
- Do not deploy, delete data, rotate secrets, run migrations, or make irreversible production changes unless explicitly authorized for this overnight run.
- Prefer progress with clean checkpoints over one giant risky attempt.
- Stop and report when blocked, when verification fails repeatedly, or when scope expands beyond the approved overnight target.

## Intake Contract

Before running overnight, establish:

```markdown
Overnight objective:

Repo / workspace:

Must-have before morning:

Allowed work:

Not allowed overnight:

Verification required:

Commit / push policy:

Stop conditions:
```

If the user does not specify commit/push policy, default to:

```text
Commit useful checkpoints locally. Do not push unless explicitly authorized.
```

## Workflow

### Phase 0: Clarify and Lock Scope

1. Use `$clarity` to rewrite the overnight objective if the request is rough.
2. Use `$scope-lock` to classify must-have, should-have, later, and non-goals.
3. Use `$preflight` before any file edits:
   - Check repo status and branch.
   - Identify current remote/default branch.
   - Record running services, env requirements, and missing tools.
   - Name risky operations.
   - Decide whether commits and pushes are allowed.

### Phase 1: Compound Existing Knowledge

Run `$ce-compound mode:headless` before planning when there was a recent solved problem, repeated friction, prior implementation attempt, or meaningful context worth preserving.

Skip pre-work compounding only when:

- There is no relevant recent solution or lesson.
- The repository has no `docs/solutions/` or equivalent knowledge store.
- The objective is urgent and compounding would not change execution.

If skipped, state why in the overnight log.

### Phase 2: Produce or Refresh the Plan

Run `$ce-plan` with the clarified objective unless a current relevant plan already exists.

The plan must provide:

- Scope boundary
- Implementation units or task sequence
- Repo-relative file paths
- Verification expectations
- Risks and dependencies
- Explicit deferred work

If a plan exists, read it and decide whether to deepen, update, or execute it as-is. Do not silently execute a stale plan.

### Phase 3: Execute Bounded Work

Run `$ce-work` against the plan or clarified work description.

Execution posture:

- Work in the smallest useful slices.
- Prefer tests or characterization before risky behavior changes.
- Commit after verified checkpoints if commits are allowed.
- Keep a running status note with completed work, checks run, and blockers.
- Use `$no-preemptive-bypass` if any bypass flag or safety override is considered.

### Phase 4: Checkpoint Loop

After each substantial slice:

```markdown
Checkpoint:

Completed:

Files changed:

Checks run:

Result:

Next slice:

Risk / blocker:
```

Continue only if:

- The next slice remains in scope.
- The tree is in a recoverable state.
- Required verification for the previous slice passed or the failure is understood.

### Phase 5: Morning Report

Before final response, use `$proof-before-done`.

Report:

```markdown
Overnight outcome:

Completed:

Not completed:

Commits:

Checks run:

Artifacts / files:

Blockers:

Risks:

Recommended next move:
```

If meaningful lessons were learned, use `$lesson-capture` or run `$ce-compound mode:headless` again to document them.

## Stop Conditions

Stop instead of continuing when:

- The repo is dirty in a way that cannot be safely attributed to this run.
- The target branch or workspace is unclear.
- Required credentials, services, or dependencies are missing.
- Tests fail repeatedly and the root cause is not clear.
- The next step requires production deployment, destructive data changes, or secret handling not explicitly authorized.
- The work no longer matches the scope lock.
- The session approaches tool/runtime limits and cannot checkpoint safely.

## Automation Guidance

If the user asks to literally continue later or overnight in this Codex thread, create a thread heartbeat automation using the available automation tool. The automation prompt should be self-contained and include:

- Objective
- Workspace
- Scope lock
- Allowed / forbidden actions
- Verification requirements
- Commit / push policy
- Expected morning report

Do not create a detached cron job unless the user explicitly asks for a detached workspace automation.

## Minimal Invocation Pattern

```text
Use $ce-overnight for this repo tonight:
[objective]

Allowed: local commits, tests, docs updates.
Forbidden: push, deploy, migrations, secret changes.
Morning report required.
```

The skill should turn that into a safe overnight cycle, not an unbounded autonomy loop.
