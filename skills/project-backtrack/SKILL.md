---
name: project-backtrack
description: Backtrack an entire project from current state through docs, plans, code structure, git history, issues, and decisions, then explain what happened and what the project is doing in plain English. Use when the user wants to understand a repo, reconstruct how a project got here, onboard quickly, audit project direction, explain current action, or turn scattered project history into a readable narrative.
---

# Project Backtrack

Use this skill to reconstruct a project's story and explain it clearly. Default to read-only inspection. Do not edit files unless the user explicitly asks for a saved report.

## Goal

Answer four questions in plain English:

1. What is this project?
2. How did it get to its current state?
3. What is happening now?
4. What should a human understand before acting next?

## Evidence Order

Gather evidence from highest signal to lowest:

1. Project instructions: `AGENTS.md`, `CLAUDE.md`, `README*`, `docs/`, `.planning/`, `ROADMAP*`, `CHANGELOG*`, `TODO*`, `VERSION`.
2. Current state: `git status`, current branch, recent diff, untracked files, active worktree.
3. Recent history: `git log --oneline --decorate --graph -n 40`, recent commits by date, changed-file stats.
4. Structure: top-level folders, package manifests, app entry points, test folders, CI config.
5. Plans and artifacts: plan docs, specs, ADRs, UAT reports, review reports, design docs.
6. Optional external state only when available and relevant: issue tracker, PRs, deployment logs, connected docs.

Prefer `rg` and `rg --files` for file discovery.

## Backtracking Workflow

1. **Orient**
   - Identify repo root, branch, dirty state, primary language/framework, and likely app purpose.
   - Note whether the repo is in a clean, dirty, or ambiguous state.

2. **Collect the Story**
   - Read the strongest project docs first.
   - Walk recent git history backward until the project direction is clear.
   - Identify major phases, pivots, migrations, unfinished plans, and recurring themes.
   - Treat docs as intent and code/git state as evidence; call out mismatches.

3. **Map the Current System**
   - Explain the main modules and how they relate.
   - Identify entry points, data flow, external services, tests, and deployment path when discoverable.
   - Keep this conceptual; avoid dumping file lists unless they help comprehension.

4. **Reconstruct Current Action**
   - Determine what the project appears to be working on now from branch name, dirty files, latest commits, TODOs, plans, and tests.
   - Separate confirmed facts from inference.

5. **Explain in Plain Text**
   - Use ordinary language.
   - Define project-specific terms the first time they appear.
   - Prefer narrative over jargon.
   - Keep technical details tied to "why it matters."

## Output Shape

Use this structure unless the user asks otherwise:

```markdown
Plain-English Project Backtrack

What this project is:

How it got here:

Current state:

Current action:

Important moving parts:

Key decisions and why they matter:

Risks, gaps, or confusing spots:

What I would inspect next:

Evidence used:
```

## Rules

- Be explicit when something is inferred.
- Do not pretend stale docs are current; compare them with code and git state.
- Do not overwhelm the user with every file. Summarize patterns and link only the most important files.
- If the project is too large for one pass, produce a high-level backtrack first, then name the next focused pass.
- If the user asks for "entire project action," emphasize current work, next likely action, blockers, and readiness.
- If the user asks for a nontechnical explanation, avoid framework names unless necessary.
