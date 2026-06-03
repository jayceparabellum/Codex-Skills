---
name: phase-complete
description: "Run the end-of-phase ceremony \" $correct on the full phase diff, optional task-list update, and a clean handoff. Use when the user says \"phase complete\", \"wrap up phase\", \"close out phase X\", or \"end of phase\". Honors the standing rule that $correct runs at end of phase (full phase scope), separately from before-push runs."
---

# phase-complete

Encodes the user's standing rule: **$correct runs at the end of every completed phase, scope = full phase diff** " separately from the pre-push run. This skill captures the phase boundary, runs the audit, and prepares a clean handoff.

## Standing rule (do not relax)

From the user's memory `feedback_correct_before_push.md`:

> Run `$correct` at end of each completed phase (scope = full phase diff) AND before each push (scope = branch-vs-default-branch). Per-phase, NOT per-commit.

End-of-phase audit is **not** the same as pre-push audit. Both are required, on different scopes. This skill handles the per-phase one.

## Step 1 " Establish the phase boundary

Ask the user (or infer if they explicitly stated it):

- **Where did the phase start?** Acceptable answers:
  - A commit SHA (e.g. "started at `81490744c`")
  - A label like "branch start" ' use `git merge-base origin/HEAD HEAD` (the merge-base with the repo's default branch)
  - "Last phase-complete" ' use the most recent commit whose message contains `phase` or that matches a user-supplied pattern
- **What is this phase called?** Used in the report and any commit message that follows. Optional but helpful.

If the user cannot specify the boundary, ask. Do not guess. The audit scope determines the audit value.

Compute the diff range and confirm it back:

```bash
git log --oneline <start-sha>..HEAD
git diff --stat <start-sha>...HEAD
```

> "Phase audit will cover N commits, M files changed, +A/-D lines. Confirm to proceed."

## Step 2 " Run $correct on the phase diff

Invoke `$correct` with the explicit scope:

> Audit every change between `<start-sha>` and `HEAD`. This is a phase-completion audit, not a single-commit audit " read every file in full and verify the phase reads as a coherent whole.

If `$correct` finds and fixes issues, report them. If `$correct` reports a clean audit, record that explicitly.

## Step 3 " Update the task list (only if it exists)

Check whether `docs$task_LIST.md` exists (relative to the repo root):

```bash
ls "$(git rev-parse --show-toplevel)/docs$task_LIST.md" 2>/dev/null
```

- **If it exists:** ask the user which checklist items the phase completed. Update the file using the existing `[ ]` ' `[x]` convention. Do not invent items.
- **If it does not exist:** skip this step entirely. Do not create a task list. The user works without one for this project.

## Step 4 " Stage and offer to commit (do not auto-commit)

If `$correct` made code edits or the task list was updated:

```bash
git status --porcelain
```

Show the user what changed. Suggest a conventional-commits message in the form:

```
chore(phase): wrap up phase <name>

- $correct audit: <N issues found and fixed | clean>
- task list: <updated | n/a>
```

Do **not** commit automatically. The user owns commits per the standing branch-only rule.

## Step 5 " Decide whether to ship-check

Ask the user: "Are you about to push? If yes, the next step is `$ship-check` (which runs a separate `$correct` on branch-vs-default-branch scope plus the project's eval/test gate). If no, this phase is wrapped."

Do not run `$ship-check` automatically " it is a deliberate decision separate from phase completion.

## Step 6 " Final report

Print a concise summary:

```
phase complete: <name | unnamed>
  range:        <start-sha>..HEAD  (N commits)
  $correct:     <clean | N issues found and fixed>
  task list:    <updated: <items> | not present | unchanged>
  next step:    <suggested $ship-check | done>
```

## What this skill never does

- **Never runs `$ship-check` automatically.** End-of-phase and pre-push are separate decisions.
- **Never commits without user approval.**
- **Never creates `docs$task_LIST.md`** if it does not exist.
- **Never assumes the phase boundary.** Ask if not stated.
- **Never collapses end-of-phase `$correct` into the pre-push `$correct`** " they have different scopes and serve different purposes.
