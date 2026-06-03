---
name: scaffold
description: "Use when presearch phase is complete and user is ready to generate project documentation files - works for both greenfield and brownfield projects. Detects project type, generates context-aware docs, guardrails, and optional skill lifecycle infrastructure. For brownfield projects, runs gap analysis and drift detection, proposes changes, and merges at the section level."
---

# Scaffold Project Files

## Overview

Generate project documentation and guardrail files. Works for both **greenfield** (new project, no existing docs) and **brownfield** (existing project with some docs already in place) projects.

- **Greenfield:** generate all files from presearch findings
- **Brownfield:** run gap analysis and drift detection, propose section-level merges and patches, confirm with user before writing

All content must be derived from presearch findings or existing docs " never use placeholder or generic content.

## Step 0: Pre-Flight

Before generating anything:

1. **Check presearch completeness.** Flag missing info and stop if any of these are absent:
   - Technology decisions (language, framework, database)
   - Core requirements (what the project does)
   - Target users or use case

   **Brownfield relaxation:** If existing docs are found, these can be derived from them instead of presearch. If `TECH_STACK.md` exists ' technology decisions can be derived from it. If `PRD.md` exists ' core requirements and target users can be derived from it. Only stop if the info can't be determined from *either* presearch findings *or* existing docs.

2. **Detect project type.** Infer from presearch findings:
   - `fullstack` " backend + frontend + database (default)
   - `backend` " API/service, no UI
   - `frontend` " SPA/static site, no server
   - `cli` " command-line tool
   - `library` " reusable package/module

3. **Detect tooling environment.** Check which AI tooling is present:
   - `.claude/` or `AGENTS.md or legacy agents.md or legacy claude.md` ' generate Codex guardrails
   - `.cursor/` ' generate Cursor rules
   - Neither ' ask user preference, default to `AGENTS.md or legacy agents.md or legacy claude.md`

4. **Assess existing files.** For each file that would be generated, classify it:

   | Existing state | Action | Description |
   |---|---|---|
   | File missing or empty (0 bytes / whitespace only) | `create` | Generate entire file (same as greenfield) |
   | File exists, partial (some sections missing) | `merge-sections` | Generate only missing sections, insert at correct position |
   | File exists, complete | `patch` | Only apply drift fixes if any found |
   | File exists, complete, no drift | `skip` | No action needed |

   A section is considered present if its heading exists and the body contains at least one non-empty line beyond the heading. A heading with only whitespace or "TBD" beneath it is treated as missing.

5. **Brownfield analysis (skip if no existing files found).** If Step 4 found any existing files, run the following before generating anything:

   **5a " Gap Analysis.** For each file, compare existing headings against the expected section structure. Output a table:
   ```
   File              | Status   | Missing Sections
   docs$prd.md       | complete | "
   docs/MEMO.md      | missing  | (entire file)
   docs/TECH_STACK.md| partial  | Database Schema, API Endpoints Summary
   AGENTS.md or legacy agents.md or legacy claude.md         | partial  | Error Logging
   .env              | complete | "
   ```

   **5b " Drift Detection.** For existing files, compare content against the current codebase:

   | Check | Source of truth | What to compare |
   |---|---|---|
   | Env vars | `grep` for `process.env.`, `os.environ`, `os.getenv` in code | `.env` file |
   | Dependencies | `package.json`, `pyproject.toml`, `Cargo.toml`, etc. | `docs/TECH_STACK.md` "Key Dependencies" section (LLM-assisted comparison " the section is free-form markdown, so compare package names from manifests against prose and flag likely omissions) |
   | Tech stack lock | `docs/TECH_STACK.md` | `AGENTS.md or legacy agents.md or legacy claude.md` / `.cursor/rules/tech-stack-lock.mdc` |
   | Requirement IDs | `docs$prd.md` requirement IDs | `docs$task_LIST.md` task group references, `docs/TESTING_STRATEGY.md` coverage matrix |

   Output a drift report:
   ```
   File              | Issue                                      | Suggested Fix
   .env              | RESEND_API_KEY referenced in code, missing | Add RESEND_API_KEY= to .env
   docs/TECH_STACK.md| react-leaflet in package.json, not listed  | Add to Key Dependencies > Frontend
   docs$task_LIST.md | [MVP3] references requirement not in PRD   | Orphaned ID " verify or remove
   ```

   **5c " Proposal.** Combine gap analysis + drift report into a single action plan grouped by file:
   ```
   Proposed changes:

   docs/MEMO.md         ' create (entire file)
   docs/TECH_STACK.md   ' merge-sections: add "Database Schema", "API Endpoints Summary"
                        ' patch: add react-leaflet to Key Dependencies > Frontend
   AGENTS.md or legacy agents.md or legacy claude.md            ' merge-sections: add "Error Logging" section
   .env                 ' patch: add RESEND_API_KEY
   docs$task_LIST.md    ' warn: [MVP3] is orphaned (no matching PRD requirement)

   Proceed? (y/n, or specify files to skip)
   ```
   User confirms before any writes happen. User can exclude specific files by naming them (e.g., "skip .env, TASK_LIST.md"). Granularity is per-file. Per-action granularity within a file is not supported in v1.

## Files to Create

All markdown documentation files go in the `docs/` directory. Config files and guardrails go in their standard locations.

### Core Documentation (always generated)

#### 1. `docs$prd.md` " Product Requirements Document

Structure:
- **Overview:** 1-2 sentence project summary
- **Problem Statement:** What problem this solves
- **Target Users:** Who uses this and why
- **MVP Requirements:** Checklist of must-have features (use `- [MVPx]` IDs for traceability)
- **Final Submission Features:** Stretch goals grouped by category
- **Performance Targets:** Table of metrics and targets
- **Scope Boundaries:** In scope / Out of scope lists

#### 2. `docs$task_LIST.md` " Phased Task Breakdown

Structure:
- **Phase 1: MVP** " numbered task groups, each with subtask checklists (`- [ ]`)
- **Phase 2: Polish** " enhancement tasks
- **Phase 3: Final** " evaluation, reliability, submission tasks
- Group by logical concern (setup, ingestion, storage, retrieval, UI, deployment, etc.)
- Each task group references the requirement ID it satisfies (e.g., `[MVPx]`)

#### 3. `docs/TECH_STACK.md` " Technology Stack

Structure:
- **Architecture Overview:** ASCII diagram showing data flow
- **Stack Decisions:** Table with Layer | Technology | Version | Rationale
- **Key Dependencies:** Grouped by backend/frontend with package lists
- **Environment Variables:** Template with empty values and comments

Conditional sections (include only when applicable):
- **Database Schema:** SQL DDL for tables, indexes (skip for `cli`, `library`, `frontend` without DB)
- **API Endpoints Summary:** Brief table of routes (skip for `cli`, `library`)

#### 4. `docs/MEMO.md` " Architecture Memo

Structure:
- **Project Summary:** 2-3 sentences
- **Key Architecture Decisions:** Numbered sections, each explaining WHY a choice was made over alternatives. Include specific comparisons with rejected options.
- **Processing Strategy:** How data flows through the system
- **Known Failure Modes:** Anticipated issues and mitigations

#### 5. `docs/ERROR_FIX_LOG.md` " Error & Fix Log

Structure:
- **Template:** Show the logging format (Date, Error, Context, Root Cause, Fix, Prevention)
- **Log:** Start with `*No errors logged yet.*`
- **Common Issues to Watch For:** Grouped by technology area with specific gotchas derived from the chosen tech stack

### Conditional Documentation (project-type dependent)

#### 6. `docs/USER_FLOW.md` " User Flow

**Generate for:** `fullstack`, `frontend`, `backend` (with UI consumers)
**Skip for:** `cli`, `library`

Structure:
- **Primary Flow:** ASCII box diagram showing step-by-step user journey with timing annotations
- **API Endpoints:** Request/response format for each endpoint
- **Example Queries:** Table with Query | Expected Result | Expected Answer

#### 7. `docs/TESTING_STRATEGY.md` " Testing Strategy

**Generate for:** all project types

Structure:
- **Testing Pyramid:** What percentage unit / integration / e2e
- **Coverage Targets:** Table with Layer | Target % | Tool
- **Test Categories:** What each test level covers in this specific project
- **CI Integration:** How tests run in the pipeline (or planned pipeline)
- **Requirement Coverage Matrix:** Table mapping requirement IDs (`[MVPx]`) to test files/suites

### Environment & Config

#### 8. `.env` " Environment Variables

- List all required env vars with empty values
- Group with comments by service/purpose
- Include optional vars with notes

#### 9. `.gitignore`

Ensure `.gitignore` exists. Include standard entries for the project's tech stack (.env, node_modules, __pycache__, etc.) as derived from presearch. Do NOT add `docs/` to `.gitignore` " scaffold docs should be committed so team members and CI can access them.

### Guardrails (environment-dependent)

Generate guardrails for the detected tooling environment (Step 0.3).

#### If Codex (`AGENTS.md or legacy agents.md or legacy claude.md`):

Append or create `AGENTS.md or legacy agents.md or legacy claude.md` with these sections:

```markdown
## Environment Protection
- Never modify .env without user confirmation
- Never commit .env files
- Never display API key values or hardcode secrets

## Error Logging
- Log build failures, runtime errors, API errors, DB errors, deployment errors, and anything >5 min to diagnose to docs/ERROR_FIX_LOG.md
- Do NOT log: typos, linter warnings, expected test failures

## Tech Stack Lock
[List every locked technology decision from presearch]
- Do not switch any locked technology without explicit user approval
- New dependencies require justification
```

#### If Cursor (`.cursor/rules/`):

Generate three rule files:

**`.cursor/rules/env-files-read-only.mdc`**
```yaml
---
description: Protect environment files from accidental modification or exposure
globs: [".env", ".env.*", ".env.local", ".env.production"]
alwaysApply: true
---
```
Rules: Never modify .env without user confirmation. Never commit env files. Never display API key values. Never hardcode secrets.

**`.cursor/rules/error-resolution-log.mdc`**
```yaml
---
description: Log errors and fixes to docs/ERROR_FIX_LOG.md when resolving issues
globs: ["**/*"]
alwaysApply: false
---
```
Define when to log, when NOT to log, category prefixes relevant to the project's tech stack.

**`.cursor/rules/tech-stack-lock.mdc`**
```yaml
---
description: Enforce the locked technology stack decisions from Pre-Search
globs: ["**/*"]
alwaysApply: true
---
```
List every locked technology decision. For each: state what IS locked, what NOT to switch to.

### Skill Lifecycle (opt-in)

These files are only generated if the user explicitly requests skill lifecycle infrastructure, or if the project already has `.agents/skills/` set up.

#### 10. `.agents/skills/find-skills/SKILL.md`

A project context skill for AI agents containing:
- **Context:** 1-sentence project description
- **Codebase:** Target, size, structure notes
- **Stack:** Bullet list of every technology choice
- **Key Files:** List all docs/*.md files and their purpose
- **Processing Strategy:** Brief pipeline description
- **Known Patterns:** Project-specific conventions

#### 11. `docs$skill-lifecycle.yml` " Skill Lifecycle Config

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

#### 12. Per-skill: `CHANGELOG.md` and `observations/_schema.md`

For every skill directory created, also create:

**`CHANGELOG.md`:**
```markdown
# Changelog

## v1 " <date>
**Initial version**
```

**`observations/_schema.md`:**
Standard observation schema with frontmatter fields (task, skill, skill_version, success, critical, timestamp, duration_seconds, tokens_used) and body sections (Error, Context, Files Touched, User Feedback).

## Section-Level Merge Logic

When merging sections into an existing file:

1. Parse existing headings (`##`, `###`) to build a section map with positions
2. Generate only the missing sections using presearch findings (or existing doc content for brownfield)
3. Insert each missing section after the last existing section that would precede it in the canonical section order (below)
4. Never reorder, modify, or delete existing sections
5. Preserve all existing content verbatim " only additive changes
6. Generated sections use existing file content as context where possible. If presearch findings conflict with existing content, the drift report flags the discrepancy rather than silently generating inconsistent sections.

### Canonical Section Orders

**`docs$prd.md`:** Overview ' Problem Statement ' Target Users ' MVP Requirements ' Final Submission Features ' Performance Targets ' Scope Boundaries

**`docs$task_LIST.md`:** Phase 1: MVP ' Phase 2: Polish ' Phase 3: Final

**`docs/TECH_STACK.md`:** Architecture Overview ' Stack Decisions ' Key Dependencies ' Environment Variables ' Database Schema ' API Endpoints Summary

**`docs/MEMO.md`:** Project Summary ' Key Architecture Decisions ' Processing Strategy ' Known Failure Modes

**`docs/ERROR_FIX_LOG.md`:** Template ' Log ' Common Issues to Watch For

**`docs/USER_FLOW.md`:** Primary Flow ' API Endpoints ' Example Queries

**`docs/TESTING_STRATEGY.md`:** Testing Pyramid ' Coverage Targets ' Test Categories ' CI Integration ' Requirement Coverage Matrix

**`AGENTS.md or legacy agents.md or legacy claude.md` guardrail sections:** Environment Protection ' Error Logging ' Tech Stack Lock

## ID Preservation

When existing `PRD.md` or `TASK_LIST.md` files contain requirement IDs:

1. Scan existing files to collect all used IDs and detect the ID pattern (e.g., `[MVPx]`, `[REQx]`, `[PREFIX_N]`)
2. Record the highest number used per prefix
3. New requirements get the next available ID (e.g., existing `[MVP1]`-`[MVP5]` ' new items start at `[MVP6]`)
4. Never renumber or reorder existing IDs
5. Orphaned IDs (referenced in TASK_LIST but not in PRD, or vice versa) are included in the drift report as warnings " not auto-deleted

## Execution

```
Pre-Flight (Step 0 checks 1-3, with relaxed presearch for brownfield)
    "
Assess existing files (Step 0 check 4, graduated assessment)
    "
Any existing files found? ""no""' Generate all files (greenfield, unchanged)
    ",yes
    "
Gap Analysis (Step 0 check 5a)
    "
Drift Detection (Step 0 check 5b)
    "
Present Proposal (Step 0 check 5c)
    "
User confirms ""no""' User adjusts scope, re-present
    ",yes
    "
Generate files per action plan (create / merge-sections / patch / skip)
    "
Cross-check (with orphaned ID check + drift re-verify)
    "
Print summary table (File | Action | Details)
```

1. **Pre-flight** " Run Step 0 checks 1-3. Stop if presearch is incomplete (unless brownfield relaxation applies).
2. **Assess & analyze** " Run Step 0 checks 4-5. Classify existing files, run gap analysis and drift detection, present proposal to user for confirmation.
3. **Generate files** " Execute the action plan. Create files in parallel batches where possible. Independent docs (PRD, MEMO, ERROR_FIX_LOG) can be created concurrently. Files that depend on others (TASK_LIST needs PRD requirement IDs) go after their dependencies. For `merge-sections`, use the section-level merge logic above. For `patch`, apply only the specific drift fixes.
4. **Cross-check** " After all files are created/modified, verify internal consistency:
   - Every `[MVPx]` requirement in PRD has a corresponding task group in TASK_LIST
   - Every env var in TECH_STACK appears in `.env`
   - TESTING_STRATEGY requirement coverage matrix references real requirement IDs
   - Guardrail tech stack lock matches TECH_STACK decisions
   - **Orphaned ID check** " flag `[MVPx]` IDs in TASK_LIST that don't exist in PRD (and vice versa). Report as warnings, don't auto-delete.
   - **Drift re-verify** " re-run drift detection on modified files only to confirm patches landed correctly. If still out of sync, flag in summary table rather than looping.
5. **Summary** " Print a table of all files with action and details:
   ```
   File              | Action   | Details
   docs/MEMO.md      | created  | entire file
   docs/TECH_STACK.md| merged   | added Database Schema, API Endpoints Summary sections
   AGENTS.md or legacy agents.md or legacy claude.md         | merged   | added Error Logging section
   .env              | patched  | added RESEND_API_KEY
   docs$prd.md       | current  | no issues found
   ```
   Action values: `created`, `merged`, `patched`, `skipped` (user excluded), `current` (no issues).

## Important

- Do NOT create files with generic/placeholder content " every section must reflect actual presearch findings
- Adapt category prefixes, error patterns, and stack locks to the ACTUAL project
- Required directories (`docs/`, `.cursor/rules/` or equivalent, `.agents/skills/` if opted in) must be created if they don't exist
- All markdown documentation files go in `docs/` " do NOT place them in the project root
- If the user passes `--dry-run`, list all files that would be created with their section outlines, but do not write anything. For brownfield, `--dry-run` runs the full analysis (gap analysis + drift detection) and displays the proposal, but writes no files
