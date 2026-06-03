---
name: memory-curate
description: "Walk every entry in the user's auto-memory directory, ask whether each is still true and still useful, then update or remove stale entries (and the MEMORY.md index in lockstep). Use when the user says \"curate my memory\", \"review my memory\", \"clean up memory\", or \"is anything in memory stale\". Read-only until the user approves a change."
---

# memory-curate

Each project has an auto-memory directory that accumulates entries across sessions. Some go stale: a project phase ends, a feedback rule gets superseded, a reference URL changes. This skill walks every entry, surfaces it to the user, and updates or removes it on explicit approval.

The memory directory for the current project lives under `~/.codex/projects/<encoded-project-path>/memory/`, where `<encoded-project-path>` is the absolute working-directory path with `/` replaced by `-`. Derive it for the current project rather than hardcoding it:

```bash
MEM_DIR="$HOME/.claude/projects/$(pwd | sed 's@/@-@g')/memory"
```

## Hard preconditions

1. The memory directory (`$MEM_DIR`, derived above) exists.
2. `MEMORY.md` exists in that directory.
3. The user is present to make per-entry decisions. This skill is interactive " do not run it autonomously.

## Step 1 " Enumerate

Read `MEMORY.md` and list every entry. Then list every `.md` file in the directory other than `MEMORY.md`. Report any mismatches:

- **Files in the directory not pointed to by `MEMORY.md`** " orphaned memory files. Flag these; they are likely stale or were created without index updates.
- **Index entries pointing to files that do not exist** " broken pointers. Flag these.

Do not auto-delete anything. Both states need user judgment.

## Step 2 " Per-entry review

For each memory file, in the order they appear in `MEMORY.md`:

1. **Read the file.** Note its `name`, `description`, `type`, and content.
2. **Surface it concisely to the user.** Format:
   ```
   [<type>] <name>
     <one-line summary of the rule/fact>
     <why this might be stale or worth re-confirming, if applicable>
   ```
3. **Ask one question:** "Still true and still useful?" Offer three response paths:
   - **Keep as-is** " move on.
   - **Update** " the user describes the change; you rewrite the file (and update the index line if the description shifts) and confirm.
   - **Remove** " you delete the file AND remove the index line in the same step.

Do not batch decisions. Decide each entry before moving to the next.

## Step 3 " Apply changes

For every change the user approves:

- **Update:** Edit the memory file in place. If the `description` field changes, edit the matching `MEMORY.md` line so the index hook still summarizes the file accurately.
- **Remove:** Delete the file (`rm`) AND remove its line from `MEMORY.md` in the same operation. Never leave one without the other.

After all per-entry decisions are applied:

- Re-read `MEMORY.md` and confirm every line still points to a file that exists.
- Re-list the directory and confirm every `.md` file (other than `MEMORY.md`) is referenced by the index.

## Step 4 " Triage orphans and broken pointers

For each orphan file flagged in Step 1, ask the user:

- **Add to index** " provide the one-line hook, append to `MEMORY.md`.
- **Remove** " delete the file.

For each broken pointer flagged in Step 1, ask:

- **Restore** " recreate the file (the user must provide the content; do not invent it).
- **Remove the line** " delete the index line.

## Step 5 " Final report

Print a concise summary:

```
memory curated:
  reviewed:    <N> entries
  kept:        <N>
  updated:     <N> (list names)
  removed:     <N> (list names)
  orphans resolved: <N>
  broken pointers resolved: <N>
  final entry count: <N>
```

## What this skill never does

- **Never writes new memories** that the user did not request. Curation prunes and corrects; it does not invent.
- **Never deletes a file without removing its index line** in the same operation, or vice versa.
- **Never marks an entry stale based on age alone.** Age is a hint, not a verdict " the user decides.
- **Never edits an entry's content based on its filename or description.** Read the body first.
- **Never proceeds past a "not sure" answer.** If the user is unsure about an entry, leave it as-is and move on.
