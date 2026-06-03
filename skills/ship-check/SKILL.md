---
name: ship-check
description: "Pre-push readiness checklist for the current branch. Runs $correct (branch-vs-default-branch scope), the project's eval/test gate if it has one, and a working-tree-and-conventions sweep, then reports a single PASS/FAIL with the exact list of issues. Use when the user says \"ship check\", \"ready to push\", \"pre-push check\", or \"verify before pushing\". Does NOT push. Honors the no-preemptive-bypass rule."
---

# ship-check

A composed gate that runs every pre-push verification the current project requires, in order, and reports a single PASS / FAIL with the specific failures. This skill **never pushes** and **never bypasses** anything " its job is to tell the user whether they are ready to push.

## Composes

- `$correct` " correctness audit, scope = full branch diff vs. the repo's default branch
- The project's eval/test gate, if it has one (e.g. an `$eval-gate` skill or a `test` script) " skip if the project has none
- `no-preemptive-bypass` " implicit guard; if any sub-step fails, do not suggest bypassing

## Hard preconditions

1. We are inside a git repo (`git rev-parse --is-inside-work-tree`).
2. The current branch is **not** the repo's default branch (this skill is for feature branches; pushing the default branch directly violates the user's branch-only rule).
3. The default branch is fetched recently. Detect it and fetch at the start so comparisons are accurate:

```bash
# Detect the repo's default branch (master, main, or whatever origin/HEAD points to)
DEFAULT_BRANCH=$(git symbolic-ref --short refs/remotes/origin/HEAD 2>/dev/null | sed 's@^origin/@@')
DEFAULT_BRANCH=${DEFAULT_BRANCH:-$(git remote show origin 2>/dev/null | sed -n 's/.*HEAD branch: //p')}
DEFAULT_BRANCH=${DEFAULT_BRANCH:-master}
git fetch origin "$DEFAULT_BRANCH"
```

Use `origin/$DEFAULT_BRANCH` everywhere below in place of a hardcoded branch name.

## Step 1 " Working tree state

Run:

```bash
git status --porcelain
git rev-list --left-right --count "origin/$DEFAULT_BRANCH"...HEAD
```

Report:
- Whether the working tree is clean (no uncommitted or untracked tracked-relevant files). Flag untracked files in paths the project ignores by convention (per `.gitignore` or local data directories) separately " those are fine.
- How many commits ahead and behind the default branch.

If the tree has uncommitted changes the user did not intend to ship, **stop** and ask. Pushing surprise edits is exactly the failure mode this skill exists to prevent.

## Step 2 " Branch-vs-default-branch correctness audit

Invoke `$correct` with the explicit scope: **everything that has changed on this branch compared to the default branch**, not just the most recent commit.

Pass scope explicitly when invoking. Concretely, the audit should consider:

```bash
git diff "origin/$DEFAULT_BRANCH"...HEAD
git diff "origin/$DEFAULT_BRANCH"...HEAD --name-only
```

If `$correct` finds and fixes issues, report them. If `$correct` reports a clean audit, record that explicitly.

## Step 3 " Eval / test gate (if the project has one)

If the project defines an eval or test gate " an `$eval-gate` skill, a `pnpm test` / `npm test` / equivalent local gate, or a CI-mirroring check " invoke it and capture the result:

- Gate **OK** ' continue.
- Gate **FAIL** ' record the specific failures. Do **not** suggest `--no-verify` or any bypass. The right next step is a real fix. (If the project uses snapshot-based evals and the snapshots are legitimately stale, re-record them " never skip the gate.)

If the project has no such gate, record this step as **n/a** and continue. Skip the gate only when the branch touches no code the gate covers; if unsure, run it.

## Step 4 " Static-analysis / type-check baseline (if the project uses one)

If the project uses a static analyzer or type checker with a baseline/suppression file (e.g. PHPStan's `phpstan-baseline.neon`, a TypeScript config, an ESLint baseline), verify the branch did not *add* new suppressions:

```bash
# Example for a PHPStan baseline " adapt to the project's analyzer/baseline file
git diff "origin/$DEFAULT_BRANCH"...HEAD -- phpstan-baseline.neon
```

If the diff shows additions (not just removals), flag it. New baseline entries hide errors instead of fixing them " prefer fixing the underlying issue. If the project has no such baseline, skip this step.

## Step 5 " Conventional commits lint (if the project uses Conventional Commits)

If the project follows Conventional Commits, validate every commit on the branch:

```bash
git log --format=%s "origin/$DEFAULT_BRANCH"..HEAD
```

For each subject line, check it matches `<type>(<scope>): <description>` with a valid type (`feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`). Report any that do not. If the project does not use this convention, skip this step.

## Step 6 " Final verdict

Print a single block in this exact shape (omit lines for steps that were n/a):

```
ship-check: <PASS | FAIL>

  working tree:         <clean | dirty: ...>
  ahead/behind default: <N ahead, M behind>
  $correct:             <clean | N issues found and fixed>
  eval/test gate:       <OK | FAIL: ... | n/a>
  static-analysis base: <unchanged | N new entries (BLOCKING) | n/a>
  conventional commits: <all valid | N invalid: ... | n/a>
```

Only output `PASS` if every applicable line above is green. A single FAIL anywhere ' overall FAIL.

## What this skill never does

- **Never pushes.** This is a check, not an action. Pushing is the user's call after seeing PASS.
- **Never adds `--no-verify`, `--force`, or any bypass flag** to any command.
- **Never proceeds past a FAIL.** Stop, report, hand back to the user.
- **Never modifies the working tree** beyond what `$correct` does (which itself only fixes correctness issues it finds).
- **Never assumes "M commits behind the default branch is fine."** Even 0 behind is worth reporting; the user can decide.
