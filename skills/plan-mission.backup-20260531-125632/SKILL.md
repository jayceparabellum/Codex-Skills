---
name: plan-mission
description: Deep-alignment planning workflow for turning fuzzy human goals, web app ideas, rubrics, product criteria, or build requests into frozen, machine-consumable Mission Blueprints before implementation. Use when Codex needs to interview the user, clarify requirements, map criteria to acceptance checks, validate a plan with planning sub-roles, and produce planning artifacts such as mission.md, tasks.json, doc-manifest.md, CLAUDE.md, and verification-plan.md.
---

# Plan Mission

Use this skill as the front door before building. Convert fuzzy intent into an aligned, validated build plan. Do not implement the project while using this skill unless the user explicitly asks to proceed after the blueprint is approved.

## Core Contract

Produce a Mission Blueprint only after alignment is explicit. The blueprint is a handoff contract for an executor, not a build log.

Default outputs:

- `mission.md`: product goal, users, workflows, scope, constraints, non-goals, decisions, and open risks.
- `tasks.json`: dependency-aware work graph. See `references/tasks-schema.md` before writing it.
- `doc-manifest.md`: source inputs, rubrics, assumptions, and artifacts the executor must read.
- `CLAUDE.md`: lean execution guidance, project-specific constraints, and quality bar.
- `verification-plan.md`: acceptance checks, rubric coverage, tests, QA steps, and human review points.

When writing to disk, place these files under `<target>/.plan/` unless the user specifies another location. If no target project is clear, provide the blueprint inline and ask where to save it later.

## Six-Phase Flow

1. Intake
   - Capture the user's goal, rubric, success criteria, target audience, constraints, examples, deadline, and preferred stack.
   - Normalize messy pasted text and preserve original requirements in `doc-manifest.md`.
   - Identify missing information that could materially change the plan.

2. Interview
   - Ask one question at a time.
   - Prefer concrete tradeoff questions over broad prompts.
   - Stop interviewing when remaining unknowns can be handled as explicit assumptions.
   - Avoid asking about implementation details the agent can safely infer from repo context.

3. Research and Sub-Role Review
   - Use these seven planning lenses. If subagents are available, dispatch them; otherwise simulate the passes explicitly and label their findings.
   - `audience-analyst`: target users, jobs-to-be-done, accessibility, usage context.
   - `requirements-elicitor`: functional requirements, non-functional requirements, constraints, non-goals.
   - `scope-researcher`: external facts, comparable patterns, library/platform fit, current constraints.
   - `creative-strategist`: differentiated product ideas, interaction model, design direction.
   - `dag-architect`: task graph, dependencies, sequencing, interfaces, handoff boundaries.
   - `risk-analyst`: ambiguity, security, privacy, scope creep, delivery risks.
   - `verification-planner`: acceptance criteria, rubric mapping, test strategy, QA plan.

4. Mission Understanding
   - Reflect the plan back before finalizing.
   - Group by product intent, audience, scope, UI/UX, data, technical approach, validation, risks, and non-goals.
   - Ask for per-part sign-off when the plan is large or ambiguous.
   - Revise until the user confirms alignment or the next step is obvious from their feedback.

5. Blueprint Freeze
   - Write the final artifacts.
   - Convert requirements into traceable acceptance criteria.
   - Convert implementation work into a dependency-aware task graph.
   - Mark assumptions explicitly with owner and verification method.

6. Validation Gate
   - Validate every rubric item has at least one requirement, task, and verification check.
   - Validate every task has an output, acceptance check, dependencies, and no hidden prerequisite.
   - Validate the plan avoids implementation work during planning.
   - If blocking gaps remain, return to the interview or Mission Understanding phase.

## Interview Protocol

Ask concise, one-question-at-a-time prompts. Use this order:

1. What outcome should exist when this is done?
2. Who is it for and what will they do first?
3. What criteria or rubric must it satisfy?
4. What is explicitly out of scope?
5. What constraints matter: stack, data, hosting, auth, privacy, budget, timeline?
6. What examples, references, or existing files should shape it?
7. What would make the result feel wrong even if it technically works?

When the user provides enough detail, stop asking and draft the Mission Understanding. If the user says "just decide," make conservative assumptions and label them.

## Planning Standards

- Preserve user intent before optimizing for implementation convenience.
- Treat rubrics as contract terms, not suggestions.
- Separate facts, assumptions, decisions, and recommendations.
- Keep implementation tasks small enough for an executor to complete and verify independently.
- Prefer repository-native patterns when a codebase exists.
- Make non-goals explicit to prevent scope drift.
- Include accessibility, responsive behavior, error states, empty states, and loading states for web apps unless clearly irrelevant.
- Include verification that can be run by an agent and review that needs a human.

## Output Shape

For inline responses, use this order:

1. Mission Understanding
2. Open Questions or Assumptions
3. Rubric Coverage
4. Task Graph Summary
5. Verification Plan
6. Approval Request

For saved files, create:

```text
.plan/
  mission.md
  tasks.json
  doc-manifest.md
  CLAUDE.md
  verification-plan.md
```

Read `references/tasks-schema.md` before authoring `tasks.json`.

## Approval Rule

Do not call the plan frozen until one of these is true:

- The user explicitly approves the Mission Understanding.
- The user asks to save/freeze the plan.
- The request is small enough that all material assumptions are obvious and documented.

If the user asks to build after approval, use the blueprint as the execution contract.
