# Six Sigma Verification Harness

Use this reference for higher-risk plans or when the fast harness finds gaps.

## 1. Define Gate

Purpose: Verify that the plan solves the right problem.

Check:

- Problem statement names the defect, pain, delay, waste, error, customer harm, or process gap.
- Customer, stakeholder, process owner, and decision owner are named.
- CTQ or requirement translates customer/stakeholder need into measurable criteria.
- Scope and non-goals prevent uncontrolled expansion.
- Success metric is observable and has a target.
- Constraints include compliance, security, cost, time, systems, and organizational boundaries.

Common findings:

- Goal is phrased as an activity instead of an outcome.
- Customer need is assumed but not validated.
- Success metric has no defect definition, threshold, or owner.
- Plan lacks a process owner who can sustain the result.

## 2. Measure Gate

Purpose: Verify that current performance and future improvement can be measured reliably.

Check:

- Baseline performance is known or the plan includes baseline capture.
- Defect/error/opportunity definitions are explicit.
- Data source, sample, capture method, and time window are identified.
- Measurement system is credible for the decision being made.
- Data quality risks are named: missing data, manual entry, delayed access, skew, compliance limits, or inconsistent definitions.
- For software/AI plans, tests and telemetry map to the same user-visible behavior as the acceptance criteria.

Common findings:

- Plan relies on anecdotes without data.
- Test plan verifies implementation details but not the quality characteristic.
- Baseline and target use different measurement methods.
- No negative, edge, or regression case exists.

## 3. Analyze Gate

Purpose: Verify that proposed work addresses likely causes, not symptoms.

Check:

- Root causes are supported by data, logs, user research, process mapping, code evidence, or experiments.
- Alternatives were considered before selecting the solution.
- Risk analysis covers failure modes, effect, cause, current control, severity, occurrence, detection, mitigation, and owner.
- Interfaces and handoffs are explicit: modules, APIs, prompts, schemas, jobs, queues, teams, or manual steps.
- CoQ/CoPQ thinking is applied when cost, waste, rework, defects, or customer dissatisfaction are relevant.

Common findings:

- Solution appears before cause analysis.
- Plan treats all defects as equal instead of prioritizing customer or cost impact.
- Risk controls are detection-only and do not prevent recurrence.
- Human workflow changes lack training, SOP, or accountability.

## 4. Improve Gate

Purpose: Verify that the implementation approach can safely prove the improvement.

Check:

- Selected solution maps directly to analyzed causes.
- Cost/benefit or effort/impact rationale is present.
- Pilot or limited rollout is planned for high-cost, hard-to-reverse, broad, or risky changes.
- Pilot defines population, time box, entry criteria, exit criteria, measurement, feedback capture, and rollback.
- Implementation plan includes documentation, training, transition, and signoff.
- Verification includes automated tests, manual UAT, browser/API/data checks, or operational checks appropriate to the work.

Common findings:

- Full rollout is planned before evidence that the solution works.
- Pilot has no decision rule.
- Training and documentation are deferred until after handoff.
- Acceptance criteria describe tasks completed, not outcomes achieved.

## 5. Control Gate

Purpose: Verify that the improvement will persist after the project team leaves.

Check:

- Control plan exists for each CTQ or critical process step.
- Control plan includes metric, specification/limit, unit, measurement method, sample size, frequency, owner, record location, and corrective action.
- Monitoring is appropriate: dashboard, control chart, scheduled review, alert, audit, or manual checklist.
- Corrective action is executable by the process owner where possible.
- Process owner and business team have the tools, training, SOPs, and permissions needed to maintain the change.
- Leadership/sponsor is informed of results, residual risks, and future improvement ideas.
- Lessons learned and next improvement candidates are captured.

Common findings:

- Plan ends at deployment rather than sustained control.
- Metric exists but no one owns it.
- Alert detects failure but no corrective action is documented.
- Process owner cannot maintain the control without the project team.

## Readiness Decision

Use the lowest tollgate status to decide readiness:

- `Ready`: all gates pass, or remaining low/medium issues are tracked and do not affect correctness or verification.
- `Ready with conditions`: no blockers remain, but specific conditions must be addressed before execution, rollout, or handoff.
- `Not ready`: any blocker remains, or the plan lacks measurable outcomes, reliable evidence, risk controls, verification, or sustaining controls.

## Agent Behavior

- Produce findings first.
- Ground each finding in a plan step, source file, missing artifact, or named assumption.
- Prefer small corrective plan changes over broad rewrites.
- Do not invent measurements when the source plan lacks them.
- Ask questions only when the answer changes readiness.
