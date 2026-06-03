---
name: clarity
description: Clean up rough, rushed, ambiguous, or error-prone user prompts into clear, efficient instructions before Codex acts. Use when the user asks to clarify, rewrite, tighten, clean up, translate, improve, or structure their prompt; when the user's request has typos or unclear intent; or when better communication would reduce erroneous output.
---

# Clarity

Use this skill as a prompt cleanup layer. Preserve the user's intent and voice while converting rough input into an instruction that is easier for Codex or another AI agent to execute correctly.

## Operating Rules

- Preserve intent; do not invent requirements.
- Correct obvious spelling, grammar, and wording issues silently.
- Make implicit context explicit when it is strongly implied.
- Mark uncertain assumptions instead of pretending they are facts.
- Ask at most three clarifying questions only when the missing information would materially change the answer or work.
- Keep the cleaned prompt shorter than the original when possible.
- If the user asks for action after cleanup, show the cleaned prompt briefly, then proceed.

## Cleanup Workflow

1. Identify the user's core objective.
2. Extract known context, constraints, files, systems, deadlines, and desired output.
3. Remove filler, duplicated phrasing, and conflicting wording.
4. Convert the request into direct instructions.
5. Add acceptance criteria or output format when useful.
6. List assumptions only if they affect execution.
7. Flag missing information only if it blocks accurate work.

## Output Shapes

For prompt cleanup only:

```markdown
Cleaned prompt:

[rewritten prompt]

Assumptions:

- [only if needed]

Questions:

- [only if needed]
```

For cleanup plus execution:

```markdown
Cleaned prompt:

[rewritten prompt]

I will proceed with this interpretation.
```

Then act on the cleaned prompt.

## Clean Prompt Pattern

Use this structure when the request is complex:

```markdown
Objective:

Context:

Constraints:

Tasks:

Acceptance criteria:

Output format:
```

Use a single concise paragraph when the request is simple.

## Error Reduction Checklist

Before finalizing the cleaned prompt, check:

- Is the desired outcome explicit?
- Is the scope clear?
- Are important constraints included?
- Are ambiguous pronouns replaced with specific nouns?
- Are typos corrected?
- Are commands separated from background context?
- Is the requested deliverable clear?
- Are assumptions labeled?

## Examples

Rough:

```text
fix the thing with auth and make sure it dont break prod
```

Cleaned:

```text
Investigate and fix the authentication issue without introducing production regressions. First identify the failing auth flow, then implement the smallest safe fix. Verify with targeted auth tests, a local login/logout check, and any available production-equivalent smoke checks. Report the root cause, files changed, checks run, and remaining risk.
```

Rough:

```text
i need this pushed but check stuff first
```

Cleaned:

```text
Before pushing, inspect the current git status, review the staged and unstaged changes, run the relevant test/build checks, summarize any risks, then commit and push only if verification passes.
```

## Boundaries

Do not turn a simple request into bureaucracy. If the user asks a direct question, answer directly unless cleanup would materially reduce error.

Do not overwrite the user's intent with a more impressive task. The best cleaned prompt is the clearest version of what they actually asked.
