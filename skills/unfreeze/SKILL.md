---
name: unfreeze
description: "Lift the Hetzner deploy freeze after Gauntlet grader feedback is received \" confirm feedback, run $ship-check, push to origin/master with explicit per-push authorization, push to the hetzner remote with explicit per-push authorization, then remove the freeze memory entry. Use when the user says \"unfreeze\", \"lift the freeze\", \"deploy now\", or \"feedback received\". Each push requires its own per-instance \"yes."
---

# unfreeze

Reverse the Hetzner deploy freeze defined in the user's memory `project_hetzner_deploy_freeze.md`. The freeze blocks pushes to `origin/master` and the `hetzner` remote until Sunday's submission has been graded **and** grader feedback has been received. This skill is the controlled exit.

## Standing constraints (still in force during this skill)

1. **Per-push authorization.** Each push to a protected remote (`origin/master`, `hetzner`) requires its own explicit "yes." Do not infer authorization from "go ahead and unfreeze."
2. **No preemptive bypass.** Never add `--force`, `--no-verify`, or any other flag before the bare command has actually run.
3. **$ship-check must PASS** before any push to a protected remote. A FAIL stops the unfreeze.

## Step 1 " Confirm grader feedback was actually received

Ask the user, plainly:

> The freeze lifts only after grader feedback has been received. Has feedback arrived for the Sunday submission?

Acceptable answers:
- **Yes, here it is** (user pastes or summarizes feedback) ' continue.
- **Yes, but I have not read it yet** ' recommend reading it first; the feedback might change what gets pushed.
- **No, but I want to push anyway** ' **stop**. The freeze rule is explicit. Tell the user the freeze still applies and that they would need to override the rule explicitly to proceed.

Do not assume "the user invoked $unfreeze" implies feedback was received.

## Step 2 " Run $ship-check on the branch being deployed

Ask which branch is going to master. Most commonly this is the active feature branch.

Invoke `$ship-check` on that branch. The result must be **PASS** to continue.

If FAIL:
- Report the specific failures.
- Stop. Do not push to any protected remote.
- Recommend fixes; loop back to Step 2 once the user is ready.

Note: the master branch CI has a known whitespace failure that the freeze intentionally left unfixed. After feedback, that fix may need to land before pushing " surface this to the user during ship-check if the conventional-commits or working-tree check catches it.

## Step 3 " Push to origin/master (with explicit per-push authorization)

Tell the user exactly what is about to happen:

> About to push branch `<name>` to `origin/master`. This is a protected remote under the freeze rule. Confirm to proceed.

Wait for explicit "yes." Do not accept implicit confirmation.

Determine the merge mechanism the user wants:
- **Fast-forward merge** if the branch is 0 commits behind master (run `git rev-list --left-right --count origin/master...HEAD` to confirm).
- **PR merge on GitHub** if the user prefers a PR-based flow. In that case, this skill does not push directly; it stops here and tells the user to merge the PR through the UI.
- **Explicit local merge then push** if the user wants the merge commit. Confirm the exact merge strategy.

Run the bare push command (no flags):

```bash
git push origin <branch>:master
```

If a hook fails, **do not bypass.** Apply `$no-preemptive-bypass`: read the failure, fix the cause, retry the bare command. The user must explicitly authorize any bypass on a per-command basis.

## Step 4 " Push to the hetzner remote (separate per-push authorization)

This is a **separate** authorization request. Do not bundle it with Step 3.

Tell the user:

> About to push to the `hetzner` remote (production deploy). This is a separate protected remote. Confirm to proceed.

Wait for explicit "yes."

Verify the remote exists:

```bash
git remote -v | grep hetzner
```

If the `hetzner` remote is not configured, stop and tell the user. Do not invent a URL.

Run the bare push:

```bash
git push hetzner master
```

(Or whichever branch/ref the user specifies. Confirm.)

Stream the output so the user sees the deploy progress.

## Step 5 " Remove the freeze memory entry

Once both pushes succeed, remove the freeze memory in two operations:

1. Delete the file: `/Users/the uservoegeli/.claude/projects/-Users-the uservoegeli-openemr/memory/project_hetzner_deploy_freeze.md`
2. Remove the matching line from `MEMORY.md` (the `- [Hetzner deploy freeze](project_hetzner_deploy_freeze.md) " ...` line).

Both operations must happen in the same step. Never delete one without the other.

Confirm to the user that the freeze memory has been removed.

## Step 6 " Final report

Print a concise summary:

```
unfreeze complete:
  feedback received:    yes
  $ship-check:          PASS
  pushed to origin:     <branch> ' master  (commit <sha>)
  pushed to hetzner:    master  (commit <sha>)
  freeze memory:        removed
```

## What this skill never does

- **Never pushes without per-push confirmation** for each protected remote. Two pushes = two confirmations.
- **Never adds `--force` or `--no-verify`** preemptively. Bypass requires its own explicit authorization per the `no-preemptive-bypass` rule.
- **Never removes the freeze memory before both pushes complete.** If anything fails partway, the memory stays " the freeze is still active.
- **Never assumes feedback was received** based on the skill being invoked.
- **Never silently fast-forwards** if the branch is behind master. Show the state and let the user decide.
