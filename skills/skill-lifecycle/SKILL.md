---
name: skill-lifecycle
description: "Observe, inspect, amend, and evaluate skills for continuous improvement - use after skill failures, to analyze skill health, or to propose skill amendments"
---

# Skill Lifecycle

Continuous improvement loop for skills. One skill, four subcommands.

## Usage

```
$skill-lifecycle observe <skill-name> ["optional feedback"] [--critical]
$skill-lifecycle inspect <skill-name>
$skill-lifecycle amend <skill-name>
$skill-lifecycle evaluate <skill-name>
```

## Config

Read `docs$skill-lifecycle.yml` from the project root for thresholds and settings. If it does not exist, use these defaults:

```yaml
observe:
  auto: true
  fields: [task, skill, skill_version, success, critical, timestamp, duration_seconds, tokens_used, error, user_feedback, files_touched]

inspect:
  failure_threshold: 3
  critical_immediate: true
  lookback_window: 20

amend:
  branch_prefix: "skill-amend/"
  branch_base: "main"
  auto_apply: true

evaluate:
  min_runs_after_amend: 5
  improvement_threshold: 0.3
```

## Bootstrap

If the target skill directory lacks `observations/` or `CHANGELOG.md`, create them automatically before proceeding:

1. Create `<skill>/observations/_schema.md` with the standard schema
2. Create `<skill>/CHANGELOG.md` with `## v1 " <today's date>` and `**Initial version**`

This allows skill-lifecycle to work on any skill, not just those created by scaffold.

---

## Subcommand: `observe`

**Purpose:** Record a skill execution outcome.

**When it runs:**
- Automatically after a skill fails (via the Post-Execution block in skill SKILL.md files)
- Manually by the user to record feedback on any run

**Behavior:**
1. Bootstrap observation infrastructure if missing (see Bootstrap above)
2. Gather observation data:
   - `task`: what the user asked for (from conversation context)
   - `skill`: the skill name (from argument)
   - `skill_version`: current version from CHANGELOG.md
   - `success`: true if manual feedback on a success, false if recording a failure
   - `critical`: true if `--critical` flag passed, or if error matches critical criteria (data loss, corruption, security issue, or output contradicting skill instructions)
   - `timestamp`: current time, ISO 8601
   - `duration_seconds`: estimated from conversation timing
   - `tokens_used`: estimated from conversation context
   - `error`: error description (from conversation context or user input)
   - `user_feedback`: the optional feedback string if provided
   - `files_touched`: files created/modified/deleted during the skill run
3. Write to `<skill>/observations/<YYYY-MM-DDTHH-MM-SS>.md` using frontmatter + body format
4. Count failed observations within `lookback_window` (default 20 most recent)
5. If count >= `failure_threshold` (default 3) or `critical: true`:
   - Notify user: "This skill has hit the failure threshold. Run `$skill-lifecycle inspect <skill>` to analyze patterns."

**Observation file format:**
```markdown
---
task: "description of what was attempted"
skill: "skill-name"
skill_version: "v1"
success: false
critical: false
timestamp: 2026-03-16T14:30:00
duration_seconds: 45
tokens_used: 12400
---

## Error
Description of what went wrong.

## Context
Relevant circumstances.

## Files Touched
- created: path/to/file.md
- modified: path/to/other.md

## User Feedback
*None*
```

---

## Subcommand: `inspect`

**Purpose:** Analyze observation history to identify patterns and determine if a skill amendment is warranted.

**When it runs:**
- Recommended by observe when failure threshold is met
- Manually by the user at any time

**Behavior:**
1. Read all observations for the skill (limited to `lookback_window`, default last 20)
2. Archive observations older than 90 days to `observations/archive/`
3. Calculate failure rate: `failed observations / total observations` within window
4. Analyze patterns across failed observations:
   - Recurring error types
   - Common contexts or preconditions
   - Specific skill steps that fail repeatedly
   - Whether failures cluster around a particular skill version
5. Output a report:
   - Total observations in window (failed / total)
   - Failure rate percentage
   - Top recurring patterns (with observation file references)
   - Whether an amendment is warranted (yes/no with reasoning)
6. If amendment warranted, ask user: "Would you like to run `$skill-lifecycle amend <skill>` to propose changes?"

**Note on failure rate:** The denominator is observation count, not total skill runs. Since only failures are auto-observed, the rate reflects recorded events. This caveat is stated in the report.

---

## Subcommand: `amend`

**Purpose:** Propose and apply a revised version of a skill based on inspect findings and observation evidence.

**When it runs:**
- After inspect recommends it and user confirms
- Manually by the user at any time

**Precondition:** Check for unmerged amendment branches matching `<branch_prefix><skill-name>-*`. If one exists, refuse and tell the user: "An unmerged amendment branch `<branch>` already exists. Merge or close it before creating a new amendment."

**Behavior:**
1. Read the current SKILL.md
2. Read all observations for the skill
3. Read the most recent inspect report (if available in conversation context)
4. Identify specific changes needed based on evidence:
   - Tighten trigger conditions
   - Add missing validation or preconditions
   - Reorder steps
   - Change output format
   - Add error handling for recurring failure modes
5. Generate a revised SKILL.md that addresses the identified issues
6. Determine the next version number from CHANGELOG.md
7. Create branch from `amend.branch_base` (default: `main`): `<branch_prefix><skill-name>-v<N>`
8. Commit the revised SKILL.md to the branch
9. Update CHANGELOG.md on the branch:
   ```markdown
   ## v<N> " <date>
   **Trigger:** <what caused this amendment " e.g., "3/7 recent runs failed at step 4">
   **Evidence:** <links to observation files>
   **Amendment:** <description of what changed and why>
   **Branch:** <branch name>
   **Evaluation:** Pending (0/N runs completed)
   ```
10. Show the diff to the user
11. Tell the user: "Amendment applied to branch `<branch>`. Review the diff and merge when ready. After merging, run the skill at least `min_runs_after_amend` times, then run `$skill-lifecycle evaluate <skill>` to check if outcomes improved."

---

## Subcommand: `evaluate`

**Purpose:** Determine whether an amendment improved skill outcomes.

**When it runs:**
- After enough runs on the amended version (default 5 observations)
- Manually by the user at any time

**Behavior:**
1. Read CHANGELOG.md to determine the current and previous version
2. Partition observations by `skill_version` field:
   - **Before cohort:** observations with the previous version
   - **After cohort:** observations with the current version
3. Check if the after cohort has at least `min_runs_after_amend` observations (default 5)
   - If not, report: "Need N more observations on v<current> before evaluation. Current: M observations."
4. Calculate failure rate for both cohorts
5. Apply improvement formula: `(old_rate - new_rate) / old_rate >= improvement_threshold`
   - `improvement_threshold` default: 0.3 (30% relative reduction)
6. Recommend one of:
   - **Keep** " improvement meets threshold. Update CHANGELOG entry with evaluation results:
     ```
     **Evaluation:** Passed " failure rate dropped from X% to Y% (Z% relative improvement)
     ```
   - **Rollback** " no improvement or regression.
     - If the amendment branch was merged: provide `git revert <merge-commit>` command
     - If not yet merged: advise closing the branch
     - Update CHANGELOG entry: `**Evaluation:** Failed " reverted`
   - **Further amend** " positive improvement but below threshold. Suggest another amendment cycle. Update CHANGELOG entry: `**Evaluation:** Partial " X% improvement, below threshold. Further amendment recommended.`

---

## Post-Execution

After this skill completes, if execution failed or produced errors:
1. Run `$skill-lifecycle observe skill-lifecycle` to record the failure
2. If the error involved data loss or contradicted skill instructions, add `--critical`
