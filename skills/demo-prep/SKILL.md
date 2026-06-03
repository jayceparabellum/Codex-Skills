---
name: demo-prep
description: "Prepare a feature for in-browser verification \" start the project's dev server, surface the URL and login, list the test cases the user should walk through, and tail the relevant error log. Use when the user says \"demo prep\", \"test this feature in browser\", \"verify in browser\", \"spin up dev for demo\", or before claiming a UI feature is done. Honors the rule that UI changes must be verified in a browser."
---

# demo-prep

Many projects' `AGENTS.md or legacy agents.md or legacy claude.md` (or README) require that UI changes be verified in a real browser before the task is reported complete " type checks and test suites verify code correctness, not feature correctness. This skill operationalizes that rule for whatever project you're in. The user does the actual clicking; this skill makes that as low-friction as possible.

If the current project has no such rule, the skill still works as a convenience for spinning up the dev server and organizing a manual test pass.

## Hard preconditions

1. We are inside the project repo. Resolve its root dynamically:
   ```bash
   REPO_ROOT=$(git rev-parse --show-toplevel)
   ```
2. The project's dev-server command is known. Find it in `AGENTS.md or legacy agents.md or legacy claude.md`, `README`, or `package.json` scripts (e.g. `pnpm dev`, `npm run dev`, or a `docker compose up` for container-based stacks).
3. Any runtime the dev server needs is available (e.g. a database running, Docker running for container stacks). If a required runtime is down, stop and tell the user " do not auto-start system services like Docker Desktop.

## Step 1 " Identify the feature under test

Ask the user (if they have not already said):

- **What feature is being demoed?** (Path / name / branch context.)
- **Is there a specific URL path** to land on? (e.g. a route like `/patients/123` or `/interface/patient_file/...`)
- **Is there specific data** to load against? (a seeded user, record, or fixture)

This information shapes the test-case list in Step 4. Do not skip it.

## Step 2 " Start the dev server

Run the project's dev-server command from the repo root. Examples (use the one this project actually uses):

```bash
# Node/Vite-style project:
cd "$REPO_ROOT" && pnpm dev      # or: npm run dev

# Container-based stack (e.g. OpenEMR's development-easy compose):
cd "$REPO_ROOT/docker/development-easy" && docker compose up --detach --wait
```

Stream the output. If the server (or any container) fails to come up healthy, stop and report what failed. Do not attempt automatic recovery.

## Step 3 " Surface the URLs and login

Print a block like this, filled in from the project's config (AGENTS.md or legacy agents.md or legacy claude.md / README / dev-server output):

```
Dev server ready.

  App:           <dev URL, e.g. http://localhost:5173/ or http://localhost:8300/>
  Other UIs:     <admin panels, db UIs, etc., if any>

  Login:         <dev credentials, if the app needs auth>

  Feature path:  <feature path from Step 1, or "n/a">
```

If the user named a feature path, append it to the App URL and present the combined link.

## Step 4 " Test-case list

Based on the feature description from Step 1, propose a numbered list:

- **Golden path** " the primary success scenario, end to end.
- **Edge cases** " empty input, maximum input, invalid input, missing permissions, concurrency if relevant.
- **Regression checks** " adjacent features the user should sanity-click to confirm nothing else broke. Pick 1"3 based on what the diff touched.

Show the list to the user. Ask: "Which case(s) do you want to walk through first?"

Do not click for the user. Do not assume the result.

## Step 5 " Tail the error log

In a separate background command, tail the project's runtime error log so the user sees errors as they happen. Use whatever the project exposes " examples:

```bash
# Container stack (e.g. OpenEMR PHP log):
cd "$REPO_ROOT/docker/development-easy" && docker compose exec openemr /root/devtools php-log

# Node project: the dev-server output itself is usually the log; or tail a logfile if one exists.
```

Run this in the background (`run_in_background=true`) so the user can interact while it streams. Tell the user how to read the log output and where to find it.

## Step 6 " After the user reports back

For each test case the user walked through, ask: "did it behave correctly?" One of three outcomes:

- **Pass** " record it. Move to the next case.
- **Fail with a clear error** " capture the symptom, the URL, and any error-log lines from the tail. Do not guess the fix; report and let the user decide whether to debug now or later.
- **Behaves but feels off** " flag it as ambiguous. The user decides whether to dig in.

When all cases are walked through, print the final result:

```
demo-prep result:
  feature:        <name>
  golden path:    <pass | fail | ambiguous>
  edge cases:     <N pass / M fail / K ambiguous>
  regressions:    <none observed | list>
  errors:         <none | summary>
  verdict:        <ready to claim done | needs follow-up>
```

## What this skill never does

- **Never claims the feature works** without the user actually walking through the cases.
- **Never auto-starts system services** like Docker Desktop. Those are system actions with side effects.
- **Never tears down the dev server** when finished. The user may want to keep poking.
- **Never skips edge cases** because the golden path passed. Golden path AND edge cases.
- **Never substitutes test-suite results** ("tests passed") for in-browser verification. They are different signals.
