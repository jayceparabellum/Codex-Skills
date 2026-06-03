---
name: ai-deployment-debugging
description: Diagnose deployment, hosting, CI/CD, environment, and runtime failures from evidence before making changes. Use when Codex needs to debug failed deploys, broken production behavior, build errors, logs, environment variables, DNS, containers, cloud services, migrations, or post-deploy regressions.
---

# AI Deployment Debugging

Use this skill when something works locally but fails in CI, staging, production, a cloud host, or a deployed runtime. Debug from evidence before editing code or config.

## Operating Rules

- Establish where the failure occurs: local, CI, build, deploy, startup, runtime, network, data, or external service.
- Collect logs before changing files.
- Compare working and failing environments.
- Treat secrets carefully: never print full secret values.
- Prefer one change at a time with a re-run after each material change.

## Debugging Flow

1. Define the symptom in one sentence.
2. Capture the exact command, URL, job, service, or deploy that fails.
3. Collect the first meaningful error and the last successful step.
4. Classify the failure:
   - Build or dependency
   - Environment variable or secret
   - Runtime startup
   - Database or migration
   - Auth/session/cookie
   - Network/DNS/TLS/CORS
   - Resource limit or timeout
   - Version mismatch
5. Compare versions, env vars, paths, ports, package manager, lockfile, and platform assumptions.
6. Make the smallest evidence-backed fix.
7. Re-run the failing step and one adjacent smoke check.

## Evidence To Gather

Use the relevant sources:

- CI logs or deployment logs
- Build output
- Runtime logs
- Environment variable names and presence, not secret values
- Health endpoint response
- Browser console and network traces
- Database migration status
- Package manager and runtime versions
- Host-specific configuration

## Smoke Checks

After a deploy fix, verify:

- The app starts without crash loops.
- The public URL or service endpoint responds.
- Auth or the primary workflow still works.
- Static assets load.
- API calls reach the expected backend.
- Database-backed pages can read/write when relevant.

## Final Report

Summarize:

```markdown
Root cause:

Fix:

Verification:

Deploy status:

Remaining risk:
```

If root cause is still uncertain, say "most likely cause" and explain what evidence supports it.
