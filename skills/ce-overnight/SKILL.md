---
name: ce-overnight
description: Run a safe overnight Compound Engineering cycle that combines ce-compound, ce-plan, and ce-work. Use when the user wants Codex to prepare or run long unattended work overnight, autopilot a project through the next safe steps until a human gate, wake up later with progress, compound prior learning before execution, produce a durable plan, execute bounded work, checkpoint, verify, and stop safely instead of drifting.
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
- In autopilot mode, continue from one safe next step to the next without asking the user again until a human gate is reached.
- Autopilot is not permission to broaden scope, bypass safeguards, push, deploy, migrate, handle secrets, or make destructive changes.

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

Autopilot policy:

Human gates:

Stop conditions:
```

If the user does not specify commit/push policy, default to:

```text
Commit useful checkpoints locally. Do not push unless explicitly authorized.
```

If the user does not specify autopilot policy, default to:

```text
Autopilot safe local work only. Stop at the first human gate.
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
   - Decide whether autopilot is enabled, and list the human gates.

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

### Phase 3: Autopilot Next-Step Selection

Use this phase when the user asks the skill to continue without being told the next step.

Autopilot chooses the next safe action from the current state, in this order:

1. If there is no current relevant plan, run `$ce-plan`.
2. If the plan is stale, incomplete, or missing verification criteria, refresh or deepen it with `$ce-plan`.
3. If there is a relevant recent solution, failure, or lesson that should inform the work, run `$ce-compound mode:headless`.
4. If the plan has an unblocked implementation unit, run `$ce-work` on the next bounded unit.
5. If implementation changed behavior, run the required local checks from the plan.
6. If checks pass and local commits are allowed, create a checkpoint commit.
7. If the current unit is complete, select the next unblocked unit.
8. If no safe unit remains, stop and produce the morning report.

Autopilot must prefer the next smallest verified step over the next largest impressive step.

#### Human Gate Detection

Stop and ask for the user when the next step involves any human gate:

- Push to any remote.
- Deploy or publish.
- Database migration, destructive data change, or irreversible operation.
- Secret, credential, token, billing, or production access.
- New dependency, paid service, or external account setup.
- Force push, hook bypass, check bypass, CI bypass, or `--no-verify`.
- Broad refactor outside the scope lock.
- Product or UX decision not resolved by the plan.
- Test failure whose fix is not clear after one focused diagnosis pass.
- Merge conflict that requires choosing between competing intents.
- Access to a different repo, machine, account, cloud project, or environment than the intake contract named.
- Any action that would surprise the user if performed while they were asleep.

When a human gate is reached, stop with:

```markdown
Human gate reached:

Why this needs you:

Current state:

Options:

Recommended choice:

Safe rollback / resume point:
```

Do not keep working around the gate by picking an unrelated task unless the scope lock explicitly defines safe fallback work.

### Phase 4: Execute Bounded Work

Run `$ce-work` against the plan or clarified work description.

Execution posture:

- Work in the smallest useful slices.
- Prefer tests or characterization before risky behavior changes.
- Commit after verified checkpoints if commits are allowed.
- Keep a running status note with completed work, checks run, and blockers.
- Use `$no-preemptive-bypass` if any bypass flag or safety override is considered.

### Phase 5: Checkpoint Loop

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
- No human gate has been reached.

If autopilot is enabled, return to Phase 3 after each checkpoint and choose the next safe step.

### Phase 6: Morning Report

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

Human gate, if any:
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
- Autopilot reaches a human gate.
- The next safe step cannot be determined from the plan, repo state, and scope lock.

## Automation Guidance

If the user asks to literally continue later or overnight in this Codex thread, create a thread heartbeat automation using the available automation tool. The automation prompt should be self-contained and include:

- Objective
- Workspace
- Scope lock
- Allowed / forbidden actions
- Verification requirements
- Commit / push policy
- Autopilot policy
- Human gates
- Expected morning report

Do not create a detached cron job unless the user explicitly asks for a detached workspace automation.

## Minimal Invocation Pattern

```text
Use $ce-overnight for this repo tonight:
[objective]

Allowed: local commits, tests, docs updates.
Forbidden: push, deploy, migrations, secret changes.
Autopilot: keep choosing the next safe local step until a human gate.
Morning report required.
```

The skill should turn that into a safe overnight cycle, not an unbounded autonomy loop.
