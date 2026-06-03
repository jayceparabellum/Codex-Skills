---
name: task
description: "Pick up the next unchecked task group from docs$task_LIST.md and start implementing it \" reads project docs for context, initializes git if needed, creates feature branches for large groups, checks off subtasks as completed"
---

# Task

Pick up the next unchecked task group from docs$task_LIST.md and implement it.

## 1. Git Initialization

If the project root is not a git repo:
1. Ask the user: "No git repo found. What would you like to name this repo?"
2. Run `git init` (rename directory if the user wants a different name)
3. Stage all files (`git add -A`) " `.gitignore` controls exclusions
4. Create initial commit: "Initial scaffold commit"

## 2. Load Context

Read these scaffold docs from `docs/` before coding:
- `docs$prd.md` " requirements and scope
- `docs$task_LIST.md` " phased tasks with checklists
- `docs/TECH_STACK.md` " stack decisions, dependencies, architecture
- `docs/USER_FLOW.md` " user journey, API endpoints
- `docs/MEMO.md` " architecture decisions and rationale
- `docs/ERROR_FIX_LOG.md` " known gotchas and past errors

If `docs/ERROR_FIX_LOG.md` has entries beyond "No errors logged yet", show **all entries** to the user as warnings before starting.

## 3. Find Next Task Group

Parse docs$task_LIST.md. A task group is a numbered heading section (e.g., `### 3. Oracle Integration`).

- Find the first group with any unchecked subtasks (`- [ ]`)
- Skip groups where all subtasks are `- [x]`
- Identify groups by their heading number and name as written
- If docs$task_LIST.md doesn't exist: "No docs$task_LIST.md found. Run `$scaffold` first."
- If all tasks are checked: "All tasks in docs$task_LIST.md are complete."

## 4. Branch Strategy

Before starting implementation, count the unchecked subtasks in the group:
- **5 or more subtasks:** Create a feature branch `task/<group-number>-<group-name-slugified>` (e.g., `task/3-oracle-integration`)
- **Fewer than 5:** Work on the current branch

## 5. Implement

Work through each unchecked subtask sequentially:

1. Create a TodoWrite entry for the subtask (mark `in_progress`). TodoWrite is best-effort " proceed without it if unavailable.
2. Write the code to accomplish the subtask
3. Update docs$task_LIST.md: change `- [ ]` to `- [x]` for that subtask
4. Commit with message: `task(<group-number>): <subtask description>`
5. Mark the TodoWrite entry as `completed`
6. Move to the next unchecked subtask

**Existing code for unchecked subtasks:** If code already exists that satisfies the subtask, check it off and move on. If it partially satisfies, complete the remainder.

## 6. Failure Handling

If a build failure, test failure, or runtime error occurs during a subtask:

1. Keep the subtask unchecked in docs$task_LIST.md
2. Log the error to docs/ERROR_FIX_LOG.md using the standard template and appropriate category prefix
3. Ask the user: "Subtask failed: <description>. Error logged to docs/ERROR_FIX_LOG.md. Want me to try to fix it, skip to the next subtask, or stop?"

Do not silently continue.

## 7. Post-Group

After completing all subtasks in the group:
- Report what was completed (subtask count, files created/modified)
- If on a feature branch, ask: "Task group complete on branch `<branch>`. Merge to main now, or keep the branch?"

## 8. Next Group or Stop

If more unchecked task groups remain:
- Ask: "Task group <N>: <name> complete. Continue to the next group (<next-N>: <next-name>), or stop here?"
- If continue, repeat from step 3
- If stop, end

## Post-Execution

A skill-level failure is when `$task` itself cannot proceed " e.g., docs$task_LIST.md is unparseable, git init fails, or multiple subtasks fail consecutively.

If skill-level failure occurs:
1. Run `$skill-lifecycle observe task` to record the failure
2. If the error involved data loss or contradicted skill instructions, add `--critical`
