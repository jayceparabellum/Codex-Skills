# Codex Skills

Full mirror of Jayce's installed Codex skills.

## Current Snapshot

- Skill directories: 199
- Source: `C:\Users\jayce\.codex\skills`
- Destination in repo: `skills/`
- Synced for: Codex skill backup, sharing, migration, and cross-machine restore

## Install Or Restore

Copy one skill:

```powershell
Copy-Item -Recurse .\skills\clarity $env:USERPROFILE\.codex\skills\
```

Copy all skills:

```powershell
Copy-Item -Recurse .\skills\* $env:USERPROFILE\.codex\skills\ -Force
```

Restart Codex after installing or updating skills so they are re-indexed.

## Important Skills In This Snapshot

- `clarity`
- `preflight`
- `scope-lock`
- `proof-before-done`
- `lesson-capture`
- `ce-overnight`
- `vision-from-discovery-call`
- GSD workflow skills
- GStack workflow skills
- Compound Engineering skills
- Converted Claude-compatible skills

## Vision From Discovery Call

The `vision-from-discovery-call` skill runs the full discovery-call intake in one batch and produces:

1. `PVD.md`
2. `PRD-v1.md`
3. final `PRD.pdf` on the user's Desktop area

## Notes

This repository is intended to hold portable skill instructions and bundled resources. Runtime state, auth files, logs, caches, and local Codex databases should not be committed.
