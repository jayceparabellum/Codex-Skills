---
name: six-sigma-verification-stage
description: Six Sigma Black Belt verification and body-of-knowledge skill for auditing project plans, phase plans, implementation plans, UAT criteria, release readiness, process improvement work, and Six Sigma questions. Use during verification gates, DMAIC/DMADV tollgates, before implementation, before handoff, before release, or when an agent must apply Six Sigma Black Belt concepts including CTQ alignment, SIPOC, VoC, Lean waste, FMEA, data collection, graphical/statistical analysis, hypothesis testing, control charts, process capability, pilots, control plans, DOE, process maps, value stream maps, and sustainment checks.
---

# Six Sigma Verification Stage

## Purpose

Use this skill as a Six Sigma verification harness and Black Belt body-of-knowledge guide for any project plan, software plan, operational workflow, AI feature, release plan, process-improvement question, or statistical quality task.

The primary goal is to reduce planning error before execution by forcing the plan through customer, data, risk, control, and handoff gates. When the user asks broader Six Sigma questions, load the relevant reference file and apply only the methods that fit the user's context.

This skill is agent-neutral. Apply it in Codex, Claude, or any agent that can read `SKILL.md` and the referenced files.

## Core Principle

Do not approve a plan because it sounds plausible. Approve it only when the intended outcome is measurable, tied to customer or stakeholder needs, supported by evidence, protected against likely failure modes, and verified with a control method that can detect regression after handoff.

## Mode Selection

- Verification mode: use for plan audits, phase verification, UAT review, release readiness, handoff checks, or "is this ready?" questions. Start with the Fast Harness below.
- Body-of-knowledge mode: use when the user asks for Six Sigma concepts, Lean tools, DMAIC/DMADV guidance, statistics, control charts, DOE, Minitab workflows, process mapping, or value stream mapping. Read `references/black-belt-body-of-knowledge.md`.
- Template mode: use when the user needs an artifact such as a FMEA, control plan, pilot plan, tollgate report, or acceptance-criteria checklist. Read `references/templates.md`.
- Deep verification mode: use when the plan is high-risk, cross-functional, data-heavy, customer-facing, AI-driven, regulated, or expensive to reverse. Read `references/harness.md`.

## Fast Harness

Run these gates in order:

1. Define the target.
   - Identify the problem, intended outcome, customer or stakeholder, CTQ/requirement, scope, non-goals, and decision owner.
   - Reject vague goals that cannot be observed or measured.

2. Measure the baseline.
   - Identify current performance, defect/error definition, available data, sample source, measurement method, and data quality risks.
   - If no baseline exists, require a baseline-capture step or explain why one is unnecessary.

3. Analyze causes and risks.
   - Check whether root causes are supported by evidence rather than opinion.
   - Require a risk pass equivalent to FMEA: failure mode, effect, cause, current control, severity, occurrence, detection, and mitigation.

4. Improve with verification.
   - Confirm proposed changes map to verified causes.
   - Check pilot, limited rollout, rollback, cost/benefit, training, documentation, and signoff needs.

5. Control the handoff.
   - Require a control plan for sustaining the change: metric, limit/specification, measurement method, sample size, frequency, owner, record location, and corrective action.
   - Require monitoring, capability/readiness checks, process-owner handoff, and lessons learned.

6. Decide readiness.
   - `Ready`: measurable, evidenced, risk-controlled, and has a sustaining control plan.
   - `Ready with conditions`: execution can begin after named conditions are handled.
   - `Not ready`: missing measurable outcomes, baseline, evidence, risk controls, verification, or control plan.

## Severity

- `BLOCKER`: execution is likely to produce the wrong result, an unverifiable result, or an uncontrolled regression.
- `HIGH`: substantial risk of rework, missed requirement, weak data, unowned control, or failed handoff.
- `MEDIUM`: meaningful ambiguity, missing detail, or weak verification that should be fixed before or during execution.
- `LOW`: clarity, polish, or optional strengthening.

## Report Format

Use this output shape unless the project has a stronger local convention:

```markdown
**Six Sigma Verification Findings**
- [BLOCKER] Short title
  Gate: Define | Measure | Analyze | Improve | Control
  Evidence: file, plan step, requirement, metric, or missing artifact.
  Impact: What can fail or become unverifiable.
  Fix: Smallest practical plan change.

**Tollgate Status**
- Define: Pass/Conditional/Fail
- Measure: Pass/Conditional/Fail
- Analyze: Pass/Conditional/Fail
- Improve: Pass/Conditional/Fail
- Control: Pass/Conditional/Fail

**Verification Gaps**
- Missing data, tests, UAT, pilot, monitoring, rollback, training, documentation, or handoff checks.

**Readiness**
Ready with conditions: one-sentence rationale.
```

## Supporting References

- Use `references/harness.md` for the full Six Sigma verification harness and tollgate questions.
- Use `references/templates.md` for report, FMEA, control plan, pilot, and acceptance-criteria templates.
- Use `references/black-belt-body-of-knowledge.md` for the comprehensive Six Sigma Black Belt body-of-knowledge reference derived from the PDF.
- Use `references/source-notes.md` for source grounding from the Six Sigma Black Belt PDF used to create this skill.
