---
name: dev-setup
description: "Walk a new developer through installing every CLI tool the project's workflows assume \" local-dev, infra/deploy, and PR tooling \" and verify each one. The canonical list of required tools lives in the repo's AGENTS.md or legacy agents.md or legacy claude.md under \"Required Developer Tools\"; this skill reads from that list (it is not duplicated here). Use when the user says \"set up my dev environment\", \"what tools do I need\", \"I'm new to this repo\", or invokes $dev-setup. Also offers an optional sweep for CLI tools referenced in the repo that are missing from AGENTS.md or legacy agents.md or legacy claude.md."
---

# Set up developer tools

Guides a new developer through installing the CLI tools required to develop, test, and deploy the current repository. The canonical tool list lives in the repo's `AGENTS.md or legacy agents.md or legacy claude.md` under **Required Developer Tools** " that is the single source of truth. This skill reads the list at runtime; do not maintain a parallel list here.

## Step 0: Preflight

- Confirm we're inside the project's working tree (`git rev-parse --show-toplevel`). If not, stop and ask.
- Confirm `AGENTS.md or legacy agents.md or legacy claude.md` exists and contains a `## Required Developer Tools` heading. If it doesn't, stop and tell the user " the skill cannot proceed without that source-of-truth section. (Offer to help create one, e.g. via `$scaffold`, if they want.)
- Detect the host OS to pick the right install hint format:
  - `uname -s` ' `Darwin` = macOS, `Linux` = Linux. On Linux, also probe `/etc/os-release` for the distro family (Debian/Ubuntu vs. Fedora/RHEL vs. Arch).
  - On Windows, ask the user whether they're using WSL2 or native PowerShell " the answer changes every install instruction. Default to assuming WSL2 if they say "Windows" without elaboration.
- Detect the available package manager: `brew` on macOS, `apt`/`dnf`/`pacman` on Linux. Save the result; you'll use it for install hints.

## Step 1: Read the tool list

Read `AGENTS.md or legacy agents.md or legacy claude.md` and extract the tools from the markdown tables under `## Required Developer Tools`. Trust the level-3 headings in AGENTS.md or legacy agents.md or legacy claude.md, not a hard-coded list. A common layout has sub-sections like:

- **Local development & testing** " always required.
- **Infrastructure & deployment** " required only if the user touches infra.
- **Issue / PR (or MR) workflow** " required for the `$agentic-workflow` skill.
- **Optional but recommended** " informational only.

Parse the first column of each table (the tool name) and the "Purpose"/"Notes" columns. Skip header rows and separator rows. If the table format changes in AGENTS.md or legacy agents.md or legacy claude.md, do not silently fall back to a hard-coded list " surface the parsing failure to the user and stop.

Ask the user once, up front, **which tracks they need** (adapt to whatever tracks AGENTS.md or legacy agents.md or legacy claude.md actually defines):

- "Just local dev/testing" " required + optional.
- "Local dev + infra/deploy" " required + infra + optional.
- "Everything (also PR/MR tooling)" " all tracks.

Default to "everything" if they don't pick.

## Step 2: Probe each tool

For each tool in the chosen tracks, run a presence probe and a version probe. Use the cheapest commands; do not start docker daemons or open auth sessions.

The exact tools come from AGENTS.md or legacy agents.md or legacy claude.md, but the probe pattern is the same for all: `command -v <tool>` for presence, and the tool's version flag for version. The table below is an **illustrative starting point** of common probes " adapt to the actual list and version flags:

| Tool | Presence | Version |
|---|---|---|
| `git` | `command -v git` | `git --version` |
| `docker` | `command -v docker` | `docker --version` |
| `docker compose` | `docker compose version` (no separate `command -v`) | same |
| `node` | `command -v node` | `node --version` |
| `npm` / `pnpm` | `command -v npm` / `command -v pnpm` | `--version` |
| `jq` | `command -v jq` | `jq --version` |
| `gh` / `glab` | `command -v gh` / `command -v glab` | `--version` (use whichever forge the repo uses) |
| `<language runtime>` | `command -v <e.g. php, python, go>` | its version flag " enforce any minimum AGENTS.md or legacy agents.md or legacy claude.md documents |
| `<package manager>` | `command -v <e.g. composer, uv, poetry>` | its version flag |

Group results into three buckets: **OK** (present, version satisfies AGENTS.md or legacy agents.md or legacy claude.md), **MISSING** (not on PATH), **OUT OF DATE** (present but below the documented minimum).

If AGENTS.md or legacy agents.md or legacy claude.md documents a version floor for any tool (e.g. a language runtime minimum), parse the installed version and enforce that floor " anything below it is OUT OF DATE.

## Step 3: Show install hints (per OS)

Print a clean status table first " every tool, its bucket, and the installed version if any. Then for each MISSING / OUT OF DATE tool, give a copy-pasteable install command tailored to the detected OS:

- **macOS** (Homebrew): `brew install <formula>`. Use casks for GUI apps like Docker Desktop, and vendor taps where Homebrew's bottle lags (e.g. `brew install hashicorp/tap/terraform`).
- **Linux (Debian/Ubuntu)**: prefer official vendor repos for fast-moving tools (`docker`, cloud CLIs, `terraform`, forge CLIs) over distro packages, which lag. Apt for stable basics (`git`, `jq`, language runtimes).
- **Linux (Fedora/RHEL)**: same idea with `dnf`, plus vendor repos for fast-moving tools.
- **WSL2**: same as the user's chosen Linux distro; remind them Docker Desktop integrates with WSL2 from the Windows host side.

Do **not** run any install commands automatically. Print them for the user to copy. Each install touches their machine globally and may want sudo, a brew prompt, or a fresh shell " that's a human decision.

## Step 4: Verify

After the user runs the installs, ask them to invoke the skill again (or to run `$dev-setup verify`) and re-run Step 2's probes. Report the new bucket counts. Stop when:

- Every required tool is in the **OK** bucket, **or**
- The user explicitly says "skip the rest" / "I'll do the others later".

For project-specific bring-up that isn't a generic CLI install, point at `CONTRIBUTING.md` and AGENTS.md or legacy agents.md or legacy claude.md's `## Local Development` (or equivalent) section rather than re-deriving steps. Typical post-install bring-up looks like (use what the project documents):

- After the runtime/package manager: install dependencies (e.g. `pnpm install`, `npm install`, `composer install`, `uv sync`).
- After Docker (container stacks): bring up the dev stack per AGENTS.md or legacy agents.md or legacy claude.md (e.g. `docker compose up --detach --wait`).
- After a pre-commit framework: install its git hook (e.g. `pre-commit install` / `prek install`).

## Step 5 (optional): Sweep for undocumented CLI tools

This step enforces the rule that *every CLI dev tool referenced in the repo should be in the Required Developer Tools list*. Run only if the user asks for it (e.g. "also check what's drifted") " it's a maintenance pass, not part of new-developer onboarding.

Heuristics (start narrow; expect false positives):

- `grep -rE '^\s*command -v ([a-z][-a-z0-9_]+)' --include='*.sh' .` " explicit presence checks.
- `grep -rE '\b(brew|apt|apt-get|dnf|pacman|pipx) install ' --include='*.md' .` " install hints in docs.
- `grep -rE '^\s*([a-z][-a-z0-9_]+) ' --include='*.sh' scripts/` and unique the leading word " actual invocations.
- The skill list itself: the project's skills directory " every skill that calls a CLI is a candidate source of new tools.

For each candidate not already in AGENTS.md or legacy agents.md or legacy claude.md's table, ask the user: "is this a tool a new developer would need? If so, I'll add it." Only update AGENTS.md or legacy agents.md or legacy claude.md after the user confirms " false positives (e.g. a tool used by a one-off CI script that isn't part of dev setup) shouldn't pollute the list.

When updating AGENTS.md or legacy agents.md or legacy claude.md, place each new tool in the right sub-section and keep the table format consistent. Don't reorganize unrelated rows.

## Final report

A short paragraph naming:

- The tracks the user opted into.
- How many tools were OK / missing / out-of-date at the start.
- The end state after Step 4 (or "remaining: " if they stopped).
- If Step 5 ran, any tools added to AGENTS.md or legacy agents.md or legacy claude.md.

## Hard rules

- **Never auto-install.** Always print commands for the user to run themselves.
- **Never duplicate the canonical list.** AGENTS.md or legacy agents.md or legacy claude.md is the source of truth. If you find yourself wanting to "just hard-code this once", stop " fix the parser instead.
- **Never silently lower a documented version floor.** If AGENTS.md or legacy agents.md or legacy claude.md sets a minimum, below that = OUT OF DATE.
- **Never substitute one forge CLI for another** (`gh` " `glab`) unless the repo says they're interchangeable " they generally are not.
- **Never run Step 5's sweep without being asked.** It's a maintenance pass, not part of onboarding.
- **When adding a new tool to AGENTS.md or legacy agents.md or legacy claude.md as part of Step 5, get explicit user confirmation per tool.** False positives in the heuristics are expected.
