---
name: no-preemptive-bypass
description: "Hard rule against bypassing safety mechanisms (pre-push hooks, type checks, pre-commit checks, CI gates, validators, eval gates, force-push, --no-verify, --skip-hooks, --no-gpg-sign, --force, --force-with-lease, etc.) before they have actually run. Always run the bare command first; let the safeguard speak; fix the underlying cause if it fails; only then \" and only with explicit per-command user authorization \" add a bypass flag. TRIGGER any time the assistant is about to type or recommend a bypass flag, OR is staging a change that the assistant predicts will fail a hook. SKIP only for commands the user has just authorized in this turn with the bypass spelled out."
---

# No preemptive bypass

## The rule

**Do not bypass a safeguard until the safeguard has actually run and the only remaining options are documented and explicit.**

Concretely: never type `--no-verify`, `--force`, `--force-with-lease`, `--skip-hooks`, `--no-gpg-sign`, `-c commit.gpgsign=false`, `git rebase --skip`, `--ignore-errors`, `--continue-on-error`, etc. on the first attempt. Always run the bare command first.

## Why this skill exists

The failure mode is always the same shape:

1. The assistant **anticipates** a hook will fail.
2. The assistant adds a bypass flag **preemptively**, "to save a step."
3. The assistant skips both the safeguard's actual signal AND the user's confirmation.

Three things that go wrong because of this:

- **The safeguard might have passed.** Predicted failures aren't actual failures. A threshold-based check (e.g., "labeled 90% pass rate") might have absorbed the new failure cleanly. Bypassing means the assistant never finds out.
- **The bypass hides a fixable problem.** If the hook *would* have failed, the right move is almost always to fix the underlying cause " record the missing snapshot, fix the test, regenerate the type. The bypass papers over the fix.
- **It violates the standing per-push authorization rule.** The user has explicitly said: bypass requires per-instance "yes." Pre-emptive bypass is using a standing authorization that doesn't exist.

The structural lesson is: **safeguards are the source of truth about whether to bypass.** If you haven't run them, you don't know.

## Procedure

### Step 1 " Run the bare command

For pushes: `git push origin <branch>` " no flags.

For commits: `git commit -m "..."` " no flags.

For any other command with safety hooks (npm test, pytest, etc.): the bare invocation.

Let the hooks run. Read every line of output.

### Step 2 " If it passes

Done. Report the result to the user. No bypass needed, ever.

### Step 3 " If a hook fails

**Do NOT add a bypass flag.** Walk through this triage:

1. **Read the failure.** What hook? What error? What file?
2. **Identify the underlying cause.** Examples:
   - Test fails ' fix the test or the code.
   - Lint fails ' fix the lint.
   - Type check fails ' fix the type.
   - Eval gate flags missing snapshot ' record the snapshot.
   - Codespell flags a typo ' fix the typo.
   - Pre-commit auto-formatter modifies files ' re-stage and re-commit.
   - CI workflow lint catches a YAML issue ' fix the YAML.
3. **Fix it.** Make the code change, regenerate the artifact, etc.
4. **Re-stage and try again with the bare command.** Loop back to Step 1.

### Step 4 " If the failure cannot be fixed in this push

This is rare. Examples that legitimately qualify:
- A hook depends on a tool not available locally (e.g., `actionlint-docker` when Docker isn't installed) AND the underlying check has been verified clean another way.
- The hook itself is broken upstream and a fix is in progress.
- The hook is gated on infrastructure currently down (e.g., a remote linter service).

Even then, **STOP and ask the user.** Tell them:
- Which hook failed
- Why it failed
- What you've verified to confirm the underlying check is actually fine
- Why you can't fix it in this push
- The exact bypass command you propose to run

Wait for an explicit "yes, bypass for this push." Do not infer authorization from prior bypasses or related context. **Each bypass is its own authorization request.**

## Anti-patterns to watch for

These are the rationalizations the assistant tends to reach for. None of them justify a bypass.

- **"I know the hook will fail because of X."** Run it anyway. You might be wrong about X, or about which hook actually catches it, or about whether the threshold absorbs it.
- **"The threshold would have absorbed this."** If the threshold absorbs it, the hook passes " so there's no need to bypass. If you're bypassing, you're admitting it would have failed.
- **"The user already approved a bypass earlier in the session."** Standing authorization for hook bypass does not exist. Each push is its own decision.
- **"This is a follow-up commit to fix the previous bypass; the hook will fail for the same reason."** Possible, but the right answer is still to run the hook first " maybe the underlying cause is now fixable.
- **"I'm in a hurry, the user will be annoyed by the back-and-forth."** The user is more annoyed by undisclosed bypasses than by a 30-second confirmation request.
- **"Force-push would unblock this rebase."** Almost never. Find the underlying conflict.
- **"--no-verify is the standard escape hatch in this codebase."** No codebase makes that an unconditional standard. Per-instance authorization still applies.

## Bypass-flag inventory (non-exhaustive)

If you find yourself typing any of these, you are about to invoke this skill. Stop.

| Flag / pattern | Tool | What it bypasses |
|---|---|---|
| `--no-verify` | git commit / push | pre-commit, pre-push hooks |
| `--force` / `-f` | git push, rm, etc. | refusal-to-overwrite checks |
| `--force-with-lease` | git push | partial protection but still overwrites |
| `--skip-hooks` | various tools | hook runners |
| `--no-gpg-sign` | git commit | signing requirements |
| `-c commit.gpgsign=false` | git -c | per-command signing override |
| `--ignore-errors`, `--continue-on-error` | various | error gates |
| `git rebase --skip` | git rebase | conflict resolution |
| `pip install --break-system-packages` | pip | package-isolation safety |
| `npm install --force` | npm | dependency-resolution safety |
| `kubectl --validate=false` | kubectl | schema validation |
| `--allow-dirty`, `--allow-empty` | various | working-tree safety checks |
| `--no-tls-verify`, `-k` (curl) | various | TLS verification |
| any `--unsafe-*`, `--dangerous-*`, `--no-safety-*` flag | various | as named |

This list is not exhaustive. The pattern to recognize: **if a flag exists to suppress a safety mechanism, that flag is governed by this skill.**

## How to use this skill

The skill is mostly a check on the assistant's own behavior. Apply it before:
- Every `git push`
- Every `git commit` where the assistant is tempted to add a flag
- Any command where a hook or validator is involved

If the user has invoked the skill explicitly via `$no-preemptive-bypass`, walk them through the procedure above on the specific command they are about to run.

The shortest possible summary, internal to the assistant: **"Run the bare command first. Always."**
