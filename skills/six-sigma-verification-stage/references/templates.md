# Templates

Use these templates as needed. Keep output concise when the plan is small.

## Verification Report

```markdown
**Six Sigma Verification Findings**
- [HIGH] Finding title
  Gate: Measure
  Evidence: The plan defines success but does not identify the baseline data source.
  Impact: Improvement cannot be proven after implementation.
  Fix: Add a baseline-capture step with metric, source, owner, and time window.

**Tollgate Status**
- Define: Pass
- Measure: Conditional
- Analyze: Pass
- Improve: Conditional
- Control: Fail

**Verification Gaps**
- No rollback check.
- No process-owner handoff.
- No control-plan metric owner.

**Readiness**
Ready with conditions: implementation can start after the baseline and control owner are defined.
```

## FMEA Lite

```markdown
| Failure mode | Effect | Cause | Current control | Severity | Occurrence | Detection | Mitigation | Owner |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
|  |  |  |  | 1-10 | 1-10 | 1-10 |  |  |
```

Use `Severity x Occurrence x Detection` as a rough risk-priority score when the team needs ranking. Do not overfit the scoring; use it to expose risk conversations.

## Control Plan

```markdown
| Process step | CTQ/metric | Spec/limit | Unit | Measurement method | Sample size | Frequency | Owner | Record location | Corrective action |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
|  |  |  |  |  |  |  |  |  |  |
```

## Pilot Plan

```markdown
Pilot scope:
Time box:
Population or segment:
Entry criteria:
Exit criteria:
Success metric:
Failure metric:
Feedback method:
Rollback plan:
Decision owner:
```

## Acceptance Criteria Quality Check

Strong acceptance criteria should be:

- Observable by a user, operator, tester, monitor, or downstream system.
- Measurable or clearly pass/fail.
- Mapped to a CTQ, requirement, defect, or risk.
- Covered by at least one verification method.
- Paired with negative or edge cases when failure is plausible.

## Readiness One-Liners

- `Ready`: The plan has measurable CTQs, baseline/evidence, risk controls, verified improvement path, and a sustaining control plan.
- `Ready with conditions`: The plan can proceed after the listed conditions are added to the plan and owned.
- `Not ready`: The plan lacks one or more critical gates needed to verify or sustain the outcome.
