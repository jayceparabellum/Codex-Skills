---
name: gauntlet-submit
description: "Walk through the Sunday Gauntlet AI submission flow \" verify the freeze rule, run $ship-check, draft the submission tweet (tagging @GauntletAI from @the userVoegeli), and explicitly do NOT push to origin/master or hetzner until grader feedback is received. Use when the user says \"submit to gauntlet\", \"prep submission\", \"submission flow\", or \"Sunday submit\"."
---

# gauntlet-submit

Orchestrate the Gauntlet AI submission with the user's standing constraints in force. The submission itself is an external act (a Twitter post linking deliverables); this skill makes sure everything that should be true at submission time is true, and protects the deploy freeze that follows.

## Standing constraints (do not violate)

These come from the user's memory and Codex-level rules. Before doing anything else, confirm them out loud to the user:

1. **Hetzner deploy freeze is active.** No pushes to `origin/master` or the `hetzner` remote until the Sunday submission is graded AND grader feedback is received. The master CI whitespace failure intentionally remains unfixed during this window.
2. **Branch-only.** Feature-branch work stays on the branch. No merge to master without an explicit per-merge "do it now."
3. **`$correct` runs end-of-phase AND before each push.** The `$ship-check` step below covers the pre-push side.
4. **Twitter post:** the user is `@the userVoegeli`; tag `@GauntletAI` in the submission post.

If the user wants to violate any of these, **stop and confirm explicitly** " do not infer authorization from the submission context.

## Step 1 " Confirm what is being submitted

Ask the user:

- Which branch is the submission? Confirm the branch name.
- What deliverables go into the tweet? (Repo URL, demo URL, eval results URL, video, etc.)
- Is the submission deadline still Sunday? If today is past Sunday or the user mentions a different deadline, ask before proceeding.

Do not guess deliverables from the codebase. Submissions are externally defined.

## Step 2 " Run $ship-check on the submission branch

Invoke `$ship-check`. The submission branch must PASS before the tweet goes out.

If `$ship-check` reports FAIL:
- Report the specific failures.
- Stop. Do not draft the submission tweet on a branch that is not ship-ready.
- Recommend fixes. Loop back to Step 2 once fixed.

## Step 3 " Confirm the freeze applies

State plainly:

> The Hetzner deploy freeze is in effect. After this submission, I will NOT push to `origin/master` or `hetzner` until you confirm grader feedback has been received. The freeze covers the master CI whitespace failure, which stays unfixed.

Wait for the user to acknowledge before continuing. This is a memory-driven rule, not a guess " it is in `project_hetzner_deploy_freeze.md`.

## Step 4 " Draft the submission tweet

Produce a draft tweet using these constraints:

- **Author handle:** `@the userVoegeli` (the user's account; do not assume otherwise).
- **Required tag:** `@GauntletAI`.
- **Length:** under 280 characters in a single tweet, OR a thread if deliverables don't fit. Confirm thread vs. single before drafting if borderline.
- **Tone:** factual; lead with what was built, then 1"2 outcome metrics if available (eval pass rates, demo result, etc.), then the link(s).
- **Links:** use the exact URLs the user provided. Do not invent URLs.
- **No hashtags** unless the user asks for them.

Show the draft to the user. Iterate until they say "post it" " do **not** post it yourself; this skill does not have Twitter access. The user posts manually.

## Step 5 " After the user confirms the tweet is posted

Tell the user clearly:

1. The freeze remains active. Do not push to `origin/master` or `hetzner`.
2. When grader feedback arrives, the unfreeze flow is `$unfreeze` (which will run `$ship-check` again, push to `origin/master`, push to `hetzner`, and remove the freeze memory entry).
3. Until then, normal feature-branch work continues; nothing is blocked except the two protected remotes.

## What this skill never does

- **Never posts the tweet.** The user posts it manually from their account.
- **Never pushes to `origin/master` or `hetzner`** during this skill. That is `$unfreeze`'s job, after grader feedback.
- **Never bypasses `$ship-check`.** A FAIL stops the submission flow.
- **Never edits the freeze memory file** in this skill. That happens in `$unfreeze`.
- **Never assumes the deadline.** If the user has not stated it, ask.
