# AI Engineer Process Skills

Codex skills for strengthening an AI engineer's core operating process:

- `clarity`: clean up rough prompts into clear, efficient instructions.
- `ai-spec-writing`: turn fuzzy requests into testable implementation specs.
- `ai-verification-discipline`: prove work is done with checks and evidence.
- `ai-deployment-debugging`: debug deploy/runtime failures from logs and environment facts.
- `scope-lock`: freeze work into must-have, should-have, later, and non-goals.
- `next-best-move`: rank possible next actions by deadline, impact, dependency, risk, and effort.
- `preflight`: check goal, assumptions, repo state, risk, proof plan, and rollback before action.
- `proof-before-done`: require concrete evidence before claiming completion.
- `lesson-capture`: extract reusable process lessons after meaningful work or friction.

## Install

Copy any skill folder from `skills/` into your Codex skills directory:

```powershell
Copy-Item -Recurse .\skills\ai-spec-writing $env:USERPROFILE\.codex\skills\
Copy-Item -Recurse .\skills\ai-verification-discipline $env:USERPROFILE\.codex\skills\
Copy-Item -Recurse .\skills\ai-deployment-debugging $env:USERPROFILE\.codex\skills\
Copy-Item -Recurse .\skills\clarity $env:USERPROFILE\.codex\skills\
Copy-Item -Recurse .\skills\scope-lock $env:USERPROFILE\.codex\skills\
Copy-Item -Recurse .\skills\next-best-move $env:USERPROFILE\.codex\skills\
Copy-Item -Recurse .\skills\preflight $env:USERPROFILE\.codex\skills\
Copy-Item -Recurse .\skills\proof-before-done $env:USERPROFILE\.codex\skills\
Copy-Item -Recurse .\skills\lesson-capture $env:USERPROFILE\.codex\skills\
```

Restart Codex after installing or updating skills so they are re-indexed.

## Toggle Usage

Invoke the skills directly when you want that mode of work:

```text
Use $ai-spec-writing to turn this assignment into a plan.
Use $ai-verification-discipline before we call this complete.
Use $ai-deployment-debugging to diagnose this failed deploy.
Use $clarity to rewrite my rough prompt before acting on it.
Use $scope-lock to control scope before implementation.
Use $next-best-move to choose what matters next.
Use $preflight before editing files.
Use $proof-before-done before calling this complete.
Use $lesson-capture to save what we learned from this task.
```

## Converted Claude Skills

The `skills/` folder also includes converted Claude skills from `claude-skills-export.zip`:

- `agentic-workflow`
- `correct`
- `demo-prep`
- `dev-setup`
- `eval-gate`
- `eval-record`
- `gauntlet-submit`
- `grill-me`
- `in-distribution`
- `llm-council`
- `memory-curate`
- `no-preemptive-bypass`
- `phase-complete`
- `prd`
- `pre-search-checklist`
- `prove-it`
- `scaffold`
- `skill-lifecycle`
- `task`
- `unfreeze`

These were normalized for Codex frontmatter, Codex-style `$skill-name` toggles, and ASCII-safe validation.
