---
name: agentic-workflow
description: "End-to-end workflow for delivering a new feature with agent assistance \" clean tree ' PRD ' branch ' PRD-only PR ' agent implements ' agent reviews ' user reviews the review ' merge. Use when the user says \"start a new feature\", \"kick off the agentic workflow\", or invokes $agentic-workflow. Orchestrates: a clean checkout, the $prd skill, branching, PR creation, an implementation Agent, a review Agent, an interactive review-of-review session with the user, and merge with explicit confirmation. Detects host (GitHub/GitLab/Gitea) from the origin URL."
---

# Agentic feature workflow

Eight steps. Run them in order. Do not skip steps. Do not collapse Step 7 into "I'll just merge it" " the human review of the agent's review is the load-bearing checkpoint.

This skill orchestrates other tools: it invokes `$prd` for Step 2, uses available subagent tooling for Steps 5 and 6, and invokes `$review` (via the Codex skill trigger) where appropriate. The skill itself should not implement the feature " delegate.

## Step 0: Preflight

Before doing anything destructive, check the basics. Stop and ask the user if any check fails.

- `git rev-parse --is-inside-work-tree` " must be `true`.
- `git remote get-url origin` " must succeed. Save the URL; you'll need it for host detection.
- Detect host from the origin URL:
  - `github.com` ' **GitHub**, CLI is `gh`.
  - `gitlab.com`, any host with `gitlab` in the FQDN, or a known self-hosted GitLab instance ' **GitLab**, CLI is `glab`.
  - `codeberg.org` or hosts running Gitea ' **Gitea**, CLI is `tea`.
  - Anything else ' **stop and ask the user** which host this is.
- Verify the CLI is installed (`command -v <cli>`). If missing, stop and ask the user to install it (or to run Step 4's PR-open command themselves). If the repo's AGENTS.md or legacy agents.md or legacy claude.md notes that forge-CLI access isn't configured for this project, surface that note and stop rather than guessing.
- Identify the main branch: `git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@'`. Fall back to `master` then `main` if that fails.

## Step 1: Land on a clean main/master

Make the working tree match origin's main branch with no local clutter.

1. **Capture the current branch** (`git rev-parse --abbrev-ref HEAD`) " useful if anything goes sideways.
2. **Stash dirty state.** If `git status --porcelain` is non-empty, run:
   ```bash
   git stash push --include-untracked --message "agentic-workflow preflight $(date -u +%FT%TZ)"
   ```
   Tell the user explicitly that you are about to stash *and what files are in scope* " quote `git status` first. Do not silently stash. The stash is recoverable via `git stash list` / `git stash pop`, but the user should know it happened.
3. **Switch and update.** `git checkout <main>` then `git pull --ff-only origin <main>`. If the pull fails (non-FF), stop and ask " do not force.

If the user objects to stashing (says they have in-progress work elsewhere), abort the skill and let them resolve it manually first.

## Step 2: Run the PRD skill

Invoke the `prd` skill via the Codex skill trigger. That skill interrogates the user in three rounds and writes `docs$prds/<NNNN>-<slug>.md`. Do not pre-fill answers from prior conversation context " the interrogation is the point.

When `$prd` finishes, capture two values from the file it created:

- The PRD number `<NNNN>` (the 4-digit prefix on the new file).
- The slug `<slug>` (everything after the prefix, before `.md`).

You will use these in Steps 3, 4, and 5.

## Step 3: Branch off main

```bash
git checkout -b prd-<NNNN>-<slug>
```

Branch naming is `prd-<NNNN>-<slug>` (e.g. `prd-0001-ai-agent-service`). If the branch already exists locally, stop and ask " never silently reset or rebase someone else's branch. If a branch with that name exists on the remote, also stop and ask.

Stage and commit the new PRD file:

```bash
git add docs$prds/<NNNN>-<slug>.md
git commit -m "docs(prd): scaffold PRD <NNNN> " <Working Title>

Assisted-by: Codex"
```

Then push with upstream tracking:

```bash
git push -u origin prd-<NNNN>-<slug>
```

The PRD file is the only thing in this commit. No code, no scaffolding stubs, no "placeholder for later" files.

## Step 4: Open a PR with only the PRD

Open the PR against `<main>` from the new branch. The PR body should contain a one-line summary plus a link/reference to the PRD path. Title format: `PRD <NNNN>: <Working Title>`.

Pick the right CLI based on Step 0's host detection:

- **GitHub** (`gh`):
  ```bash
  gh pr create --base <main> --head prd-<NNNN>-<slug> \
    --title "PRD <NNNN>: <Working Title>" \
    --body "$(cat <<'EOF'
  Tracking PR for [PRD <NNNN>](docs$prds/<NNNN>-<slug>.md). Implementation will follow on this branch.
  EOF
  )"
  ```
- **GitLab** (`glab`):
  ```bash
  glab mr create --target-branch <main> --source-branch prd-<NNNN>-<slug> \
    --title "PRD <NNNN>: <Working Title>" \
    --description "Tracking MR for [PRD <NNNN>](docs$prds/<NNNN>-<slug>.md). Implementation will follow on this branch."
  ```
- **Gitea** (`tea`):
  ```bash
  tea pr create --base <main> --head prd-<NNNN>-<slug> \
    --title "PRD <NNNN>: <Working Title>" \
    --description "Tracking PR for [PRD <NNNN>](docs$prds/<NNNN>-<slug>.md). Implementation will follow on this branch."
  ```

Capture the URL the CLI prints. You'll reference it in the final report.

If the CLI is missing or auth fails, stop. Don't fall back to a different host's CLI; don't silently skip the PR step.

## Step 5: Delegate implementation to an Agent

Use available subagent tooling (subagent_type=`general-purpose`) with a self-contained prompt that:

- Names the PRD file path (`docs$prds/<NNNN>-<slug>.md`) and the branch (`prd-<NNNN>-<slug>`) it must commit to.
- States that it is implementing this PRD's *in-scope* items only " open questions stay open, non-goals stay out.
- Tells it to commit and push as it works, **once each semantically-distinct chunk of work is done** (not after every file, not all-at-once at the end). Examples of "semantically distinct": a new service stub + its first passing test; a migration + the model that uses it; the seeder + a smoke test that runs it. Use Conventional Commit messages, `Assisted-by: Codex` trailer.
- Tells it to honor `AGENTS.md or legacy agents.md or legacy claude.md` (the project's coding standards, its code-quality/lint commands, and its documented testing workflow).
- Tells it to **stop and report** at the end " not to open or merge the PR, not to request review, not to bump versions. The skill drives those steps.
- Tells it to surface any blockers (open questions resurfacing, missing fixtures, ambiguous PRD wording) rather than guessing.

Run the implementation agent in the foreground " Step 6 depends on its output.

When the agent finishes, verify on the host:

- `git status` " working tree clean (the agent should have committed everything it touched).
- `git log <main>..HEAD --oneline` " at least one commit beyond the PRD scaffold.
- `git rev-parse @{u}` and `git rev-parse HEAD` " equal (it pushed).

If any of those fail, prompt the agent (or the user) to reconcile before moving on.

## Step 6: Delegate review to an Agent

Invoke the `review` skill via the Codex skill trigger " it is the project's documented PR-review entry point. If for some reason `review` is unavailable, fall back to using available subagent tooling (subagent_type=`general-purpose`) with a prompt that:

- References the PR URL from Step 4 and the PRD file.
- Asks for a structured review: correctness vs. PRD, adherence to the project's AGENTS.md or legacy agents.md or legacy claude.md conventions, test coverage of in-scope items, and any security concerns flagged by the codebase's conventions.
- Is told **not to push commits, not to fix issues, and not to merge** " review only, written output only.

Capture the review output. Save it for Step 7.

## Step 7: Review the review with the user (interactive)

This is the human-in-the-loop checkpoint. Present the agent's review to the user in a readable form, then walk through it with them:

- For each finding, ask: accept (fix it), reject (false positive " explain why), or defer (file as a follow-up).
- Apply accepted fixes directly (or delegate small ones to a fresh subagent call), commit and push them on the same branch.
- Reject false positives with a one-line note in your reply so the user can see the reasoning.
- Defer items get added to the PR description as a "Follow-ups" checklist (use the host's CLI to edit the PR body).

Do not move on to Step 8 until the user explicitly says "ship it" / "merge it" / "looks good". Open questions or ambiguity " stop and ask.

## Step 8: Merge

Merging is irrevocable on a shared branch. **Require explicit user confirmation in this turn** " a "go ahead" from earlier in the conversation does not count.

Once confirmed, merge using the host's CLI:

- **GitHub:** `gh pr merge --squash --delete-branch` (or `--merge` / `--rebase` if the user prefers " ask if unsure).
- **GitLab:** `glab mr merge --squash --remove-source-branch`.
- **Gitea:** `tea pr merge --style squash --delete-branch`.

Default to **squash** unless the user has expressed a different preference for this repo. After merge, return to `<main>` and `git pull --ff-only`.

If there is a stash from Step 1, remind the user it's still in `git stash list` and offer to `git stash pop` it.

## Final report

One short paragraph naming:

- The merged PR URL.
- The PRD path.
- Any deferred follow-ups recorded on the PR.
- The state of the Step 1 stash (popped, still pending, or none).

## Hard rules

- **Never skip Step 7.** The human review of the agent review is non-negotiable. If the user wants to skip it, that's a different workflow " point them at it and stop.
- **Never merge without explicit in-turn confirmation.** Earlier "yes" answers don't carry across the implementation$review steps.
- **Never silently stash.** Always show `git status` and tell the user what's about to be stashed.
- **Never let the implementation agent open or merge the PR.** Those are skill-driven steps, not agent-driven.
- **Never fall back to a different host's CLI.** If `glab` is missing on a GitLab repo, stop " do not use `gh` instead.
- **Never collapse multiple PRDs into one branch.** One PRD per invocation. If the user wants a second feature, start the skill again from Step 0.
- **Honor AGENTS.md or legacy agents.md or legacy claude.md and existing skill outputs.** The PRD that `$prd` produces is the source of truth for Step 5; don't rewrite it during implementation.
