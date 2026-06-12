---
name: ce-next-step
description: Invoke ce-work with the fixed prompt "let's implement the next step". Use when the user asks to implement the next step, continue the next implementation step, advance current CE work, or wants a shortcut for "[$ce-work](C:\Users\jayce\.codex\skills\ce-work\SKILL.md) let's implement the next step".
---

# CE Next Step

This is a thin wrapper around `ce-work`.

## Workflow

When this skill is invoked:

1. Load and follow `C:\Users\jayce\.codex\skills\ce-work\SKILL.md`.
2. Treat the user request as if it were:

   ```text
   [$ce-work](C:\Users\jayce\.codex\skills\ce-work\SKILL.md) let's implement the next step
   ```

3. Pass exactly this work description to `ce-work`:

   ```text
   let's implement the next step
   ```

4. Execute the `ce-work` workflow. Do not stop at restating or delegating the prompt.

## Scope

- This skill does not replace `ce-work`; it only supplies the common argument.
- If the user includes additional instructions, preserve them as extra context while keeping `let's implement the next step` as the core `ce-work` work description.
- If `ce-work` requires clarification, branch setup, or plan detection, follow `ce-work` exactly.
