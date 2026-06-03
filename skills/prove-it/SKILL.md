---
name: prove-it
description: "End-to-end agent-driven proof that a UI implementation actually works. Run AFTER planning, implementation, $correct, and any QA \" when the coding agent believes the task is complete and BEFORE $ship-check. Walks the exact user flow that was just built using browser automation, verifies pages load, interactions succeed, no console errors, output matches design system, then captures a final proof screenshot. If anything fails, surface it with evidence and stop \" do NOT mark complete until 100% confident. Use when the user says \"prove it\", \"verify the implementation\", \"show me it works\", \"confirm the feature works end-to-end\", or invokes $prove-it. Auto-suggest after implementation + $correct pass and before $ship-check, ONLY for UI-touching changes. STATUS: dormant until a browser automation tool is installed (see Step 1 pre-flight)."
---

# $prove-it " End-to-End Implementation Proof

Agent-driven verification gate. Implementation phase says "done." This skill proves it " or proves it isn't " with a screenshot.

This is the *agent-verifies* counterpart to [demo-prep](..$demo-prep/SKILL.md), which is *user-verifies*. Use `demo-prep` when the user will click through it himself; use `prove-it` when an agent should walk the flow and produce a proof artifact.

## Iron law

**Cannot return success without a screenshot of the working feature.** No screenshot, no proof. No exceptions. For backend-only changes with no observable UI, this skill does not apply " use logs/tests as proof and run `$correct` + `$ship-check` instead.

## Hard precondition: a browser automation tool

This skill is **dormant until** one of the following is installed and working on this machine:

- `browser-use` CLI (Python) " preferred, headed Chromium with vision
- `playwright` (Node or Python) " headed/headless, screenshots, network capture
- `mcp__playwright__*` MCP server " wired into Codex via settings.json

If none of these is present, **stop in Step 1 pre-flight** and tell the user the skill cannot run yet. Do not attempt to fake the proof with `curl` + HTML inspection.

## When to invoke

- Coding agent finished implementing a UI feature/fix on a feature branch (or master, where that workflow applies).
- `$correct` has passed.
- About to run `$ship-check` and push / deploy.
- the user explicitly asked for verification.

Do NOT invoke during planning, mid-implementation, for backend-only changes (API endpoints, data/permission resolvers, prompts), or for changes whose only surface is a CLI command.

## Step 0 " Understand what to prove

Before opening a browser, answer these from the conversation/repo:

1. **What was the goal?** Read the most recent plan file under `docs/`, the most recent commits (`git log -5 --oneline`), or scroll the conversation for the original intent. State it in one sentence.
2. **What is the user flow?** List concrete steps: URL ' click ' input ' expected result. If unclear, ask the user via AskUserQuestion.
3. **What does success look like?** What pixel/text/state on screen confirms the goal?
4. **What is the design system?** Identify the project's UI conventions " its component library, theme tokens, framework (e.g. Tailwind, Bootstrap), and any design notes or static assets. Check AGENTS.md or legacy agents.md or legacy claude.md / README and the project's styles directory. Note expected visual conventions.
5. **What URL?** Default to the project's local dev server (find the port in AGENTS.md or legacy agents.md or legacy claude.md / README " e.g. `http://localhost:5173/`, `http://localhost:8300/`). Use a staging/production URL only if the user explicitly says to verify there.

If any of these is unknowable, ask before continuing. Do not guess the flow.

## Step 1 " Pre-flight

Verify a browser tool exists. If none, stop.

```bash
HAS_TOOL=0
command -v browser-use >/dev/null 2>&1 && { echo "browser-use: OK"; HAS_TOOL=1; }
command -v playwright   >/dev/null 2>&1 && { echo "playwright (CLI): OK"; HAS_TOOL=1; }
python -c "import playwright" 2>/dev/null && { echo "playwright (python): OK"; HAS_TOOL=1; }
node -e "require('playwright')" 2>/dev/null && { echo "playwright (node): OK"; HAS_TOOL=1; }
# Check Codex settings for an MCP playwright server
grep -l "playwright" ~/.codex/settings.json ~/.codex/settings.local.json 2>/dev/null && { echo "playwright MCP: configured"; HAS_TOOL=1; }
[ "$HAS_TOOL" = "0" ] && echo "BLOCKED: no browser automation tool installed. Skill cannot run."
```

If blocked: tell the user plainly, link to the "Browser tooling" section below for install options, and exit. Do not proceed.

Verify the project's local dev server is reachable (substitute the project's dev URL):

```bash
DEV_URL="${DEV_URL:-http://localhost:8300/}"   # set to the project's actual dev URL/port
curl -s -o /dev/null -w "%{http_code}\n" --max-time 3 "$DEV_URL"
```

If not 200/302: ask the user to run `$demo-prep` first (which starts the dev server), then come back. Do not start the dev server yourself " that belongs to `demo-prep`.

If the feature requires authentication, the browser tool will need to log in with the project's dev credentials on first navigation, OR you can import an existing session cookie if the user has one.

## Step 2 " Walk the flow

Drive the chosen browser tool through each step of the flow defined in Step 0. For every step:

1. Take action (navigate, click, type, submit).
2. Wait for the page to settle (network idle or a known selector).
3. Assert the expected post-condition (URL, visible text, element state).
4. Capture a checkpoint screenshot if it's a meaningful state.
5. Read the browser console " any errors are bugs unless explicitly justified.
6. Read network responses " any 4xx/5xx on requests for the feature path are bugs.

Run the **happy path first**. Then the **most likely edge case** (empty state, validation error, auth-required redirect " pick the one most relevant to what was implemented).

For visual changes that target multiple breakpoints: verify mobile (375px) and desktop (1280px+) by resizing between captures.

## Step 3 " Compare against design

If the change is UI:

- Spacing/padding looks intentional, not arbitrary.
- Typography matches the project's conventions or design notes.
- Colors come from the theme/tokens, not random hex.
- Hover/focus/active states exist where expected.
- No layout shifts, overflow, or clipped text.
- Loading and empty states render correctly.

Name the specific tokens or theme variables used if any are referenced.

## Step 4 " The proof artifact

Take ONE final screenshot that captures the feature working in its success state. Save it under `.prove-it/<timestamp>/proof.png`:

```bash
PROOF_DIR=".prove-it/$(date +%Y%m%d-%H%M%S)"
mkdir -p "$PROOF_DIR"
echo "Proof dir: $(pwd)/$PROOF_DIR"
```

The browser tool writes screenshots into `$PROOF_DIR/`. Filename convention:

- `proof.png` " the headline screenshot
- `step-01.png`, `step-02.png`,  " checkpoints

Print the **absolute path** to `proof.png` so the user can drop it into a commit body, the Hetzner deploy note, or (when applicable) a PR description.

For master-direct workflow: the screenshot lives in the repo's `.prove-it/` tree (gitignored " add to `.gitignore` on first run) and is referenced from the commit body, not the PR. Only suggest "attach to PR" if a PR actually exists.

## Step 5 " Verdict

Produce a concise report. Format:

```
PROVE-IT REPORT
===============
Goal: <one-sentence restatement>
URL tested: <url>
Flow: <step1> ' <step2> ' <step3>

Happy path: PASS / FAIL
Edge case (<name>): PASS / FAIL
Console: clean / <N> errors
Network: clean / <N> failed requests for feature path
Visual: matches design / <issues>

Proof screenshot: <absolute path>

Verdict: SHIP / DO NOT SHIP
Confidence: <0"100>%
```

**Verdict rules:**

- `SHIP` requires: happy path PASS, console clean, network clean for the feature path, screenshot captured, confidence  95%.
- `DO NOT SHIP` for anything else. List concrete issues with file paths and reproduction steps. Do not auto-fix " surface and stop. Hand back to the implementer.

## Step 6 " Hand off

If `SHIP`:

- Print the proof screenshot path.
- Suggest: "Run `$ship-check` next, then commit + push (and redeploy if the project has a deploy step)."
- Offer to embed the screenshot path in the commit body.

If `DO NOT SHIP`:

- List each failure with: what was expected, what happened, where to look (`file:line`).
- Suggest returning to the implementer for clear bugs, or `$correct` if it looks like a regression in nearby code.

## Anti-patterns

- **Do not** declare success without taking the proof screenshot.
- **Do not** test only the path you implemented " test the path the user will actually take, including the entry point.
- **Do not** silently ignore console errors. They are bugs unless explicitly justified.
- **Do not** fix bugs in this skill. Surface and stop. Fixing belongs to the implementer or `$correct`.
- **Do not** test against a stale dev server that doesn't reflect the latest changes " verify the server picked up the implementation (rebuild/restart if needed; e.g. for a Docker-based stack, restart the relevant service).
- **Do not** mock, stub, or skip steps. Real end-to-end execution is the whole point.
- **Do not** auto-start Docker " that belongs to `$demo-prep`.
- **Do not** preemptively add `--no-verify` or skip hooks downstream. Honors the `no-preemptive-bypass` rule.

## Browser tooling " what to install when ready

Three workable paths, in order of preference:

1. **Playwright via MCP server** " best Codex integration. Add a Playwright MCP server to `~/.codex/settings.json` so the agent can drive a browser through tool calls. This is the most native option once it exists.
2. **Playwright Python or Node** " `pip install playwright && playwright install chromium`. The skill drives it via `Bash` + a small inline script. Works today, no MCP needed.
3. **`browser-use` CLI** " vision-driven browser agent. Heavier, but useful for "navigate by what you see" flows.

When one is installed, the pre-flight in Step 1 will detect it and the skill becomes live. Until then this skill exits in Step 1 with a clear "blocked" message.

## Notes

- This skill is read-only by default " it does not modify source code. The only writes are screenshots under `.prove-it/` and the verdict report.
- Add `.prove-it/` to `.gitignore` on first run if not already present.
- For backend-only or CLI-only features, the proof artifact is the actual command output and a passing test run " but say so explicitly and explain why no screenshot. In practice, prefer `$correct` + the project's test/eval gate + `$ship-check` for those.
