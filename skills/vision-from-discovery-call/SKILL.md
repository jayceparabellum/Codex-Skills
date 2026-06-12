---
name: vision-from-discovery-call
description: Run the full pre-planning vision intake from the Discovery Call Intake Form. Use when the user says "Vision from Discovery Call", wants the vision stage before planning, wants every discovery form question asked in one batch, or wants answers turned into a PVD.md before producing PRD-v1.md and the final PRD.pdf.
---

# Vision from Discovery Call

Run the vision stage before planning. Ask every question from the source discovery form in one batched prompt, collect the user's numbered replies in chat, then produce `PVD.md` as the Project Vision Document. The downstream PRD stage must produce `PRD-v1.md` and export the final PRD product as PRD.pdf to the user's Desktop area by default.

## Required Reference

Read `references/discovery-call-intake-questions.md` before asking questions. Treat it as the canonical checklist.

## Operating Rules

- Ask all questions at once in the order listed in the reference.
- Number every question exactly as shown in the reference.
- Tell the user to answer with matching numbers and to write `skip` for any item they want to skip.
- Do not skip optional fields. Optional still means include it in the batch.
- For checkbox questions, present all options and allow multiple selections when the reference says "check all that apply".
- For "choose one" questions, ask for one selection unless the user explicitly says they are unsure.
- Accept plain-language answers; do not force formality.
- If the user answers `skip`, record `Skipped by user`.
- After the user's batch reply, map every numbered answer to the matching question.
- Ask follow-up questions only for missing numbers, contradictions, or answers that would materially affect the PVD, PRD, roadmap, the "one bet that matters", or the final `PRD.pdf`.
- Ask missing/follow-up questions in the smallest possible batch, not one by one, unless there is only one issue.
- Do not create `PVD.md` until every question in the reference has an answer or explicit `Skipped by user`.
- Never provide the final project overview until every question has an answer recorded in the current conversation.
- Ask before assuming whenever a missing or contradictory answer would affect the PVD, PRD, roadmap, the "one bet that matters", or the final `PRD.pdf`.

## Output Artifact

After the complete batch intake is answered, create `PVD.md` in the current workspace unless the user gives another path.

Use this structure:

```markdown
# Project Vision Document

## Intake Metadata
- Source form: Discovery_Call_Intake_Form_BLANK.docx
- Skill: vision-from-discovery-call
- Completed:

## Raw Intake Answers

1. **Question text**
   - Answer:

## Vision Summary

## One Bet That Matters

## Problem

## Target Users

## MVP Candidate

## Phase 2 / Future Parking Lot

## Design Direction

## Technical Direction

## Integrations

## Data Sensitivity, Risks, and Trust Concerns

## Timeline and Budget

## Measurable Success

## Immediate Next Steps Before Planning

## Next-Stage Prompts
```

## PVD Requirements

The PVD must include:

- A concise project overview.
- The problem being solved.
- The target users and context.
- The user's full intake answers.
- MVP candidate scope.
- Phase 2 or future ideas parked outside the first proof.
- The key trust/risk assumption.
- Measurable success criteria.
- Immediate next steps before formal planning.

When the project involves AI prioritization, explicitly frame the one bet that matters as:

> People trust the AI's priorities.

If the intake does not support that bet, ask before using it.

## Next-Stage Prompts and Final PRD PDF

Add these exact prompts to the bottom of `PVD.md` after the PVD is complete:

```text
From PVD.md, draft PRD-v1.md as a v1-only PRD. Include: problem, 3-5 user stories (as a... / I want... / so that...), in-scope vs out-of-scope, and measurable success. Cut what isn't needed to prove the one bet that matters - that people trust the AI's priorities; park the rest as out-of-scope. Ask before assuming. After PRD-v1.md is accepted, export the final PRD product as PRD.pdf to the user's Desktop area.
```

```text
From PRD-v1.md, build a Now / Next / Later roadmap. Sequence by risk x value, not by excitement. "Now" = only what tests the riskiest assumption - do users trust the AI's priorities? One line of rationale per item; keep Next and Later deliberately loose.
```

## Final PRD Output Requirement

The process is not complete at the PRD stage until `PRD.pdf` exists. When asked to continue from `PVD.md` into the PRD stage:

1. Draft `PRD-v1.md` from `PVD.md`.
2. Ask before assuming any missing product detail.
3. Keep the PRD v1-only and focused on the one bet that matters.
4. After the PRD content is accepted or no further clarification is needed, export it as PRD.pdf to the user's Desktop area.
5. Verify that PRD.pdf exists on the user's Desktop before calling the PRD stage complete.


## Desktop Output Rule

When exporting `PRD.pdf`, place it in the user's Desktop area by default. On Windows, prefer `C:\Users\<user>\OneDrive\Desktop` when it exists; otherwise use `C:\Users\<user>\Desktop`. If the user gives a different output path, honor that path.
## Workflow

1. Load the question list from `references/discovery-call-intake-questions.md`.
2. Start a batched numbered intake in chat containing every question.
3. Ask the user to reply with matching numbers, grouped however they prefer.
4. After the user's reply, map answers to the canonical question list.
5. Identify unanswered, malformed, or contradictory items.
6. If anything is missing, ask one compact follow-up batch for only those items.
7. When all questions are answered or explicitly skipped, create `PVD.md`.
8. Include the complete raw Q&A in `PVD.md`.
9. Generate the PVD sections from the answers without inventing unsupported details.
10. If key details are still missing or contradictory, ask before writing final PVD sections.
11. End by telling the user `PVD.md` is ready, the next stage is `PRD-v1.md`, and the final PRD product of the process is PRD.pdf on the user's Desktop.

## First Message Pattern

When starting or resuming, say:

```text
Using Vision from Discovery Call. I will ask every intake question from the discovery form in one batch. Reply with the matching numbers; use `skip` for anything you want to skip. After the full intake is complete, I will produce PVD.md as the vision-stage artifact.

Questions 1/Y:
[full numbered question list]
```