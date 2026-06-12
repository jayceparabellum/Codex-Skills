# The Planner — a deep-alignment planning agent

This is a self-contained package describing (and implementing) **the Planner**: a front-door planning
agent that turns a fuzzy human goal into a frozen, machine-consumable build plan — only after working
back and forth with you until you're perfectly aligned.

It was extracted from a larger autonomous-engineering system and **decoupled** so it stands on its own.
You don't need the rest of that system to read, understand, or run it.

## What's here

| File | What it is |
|---|---|
| **`PLANNER-ARCHITECTURE.md`** | **Start here.** The full architecture in plain language: why it exists, the 6-phase flow, the interview protocol, the comprehension checkpoint, the 7 sub-agents, and the output contract. |
| `skills/plan-mission/SKILL.md` | The front-door skill — the actual prompt that drives the 6-phase flow and the hard rules. |
| `skills/plan-mission/GRILL.md` | The relentless one-question-at-a-time interview protocol the skill follows. |
| `agents/*.md` | The 7 planner sub-agents (audience-analyst, requirements-elicitor, scope-researcher, creative-strategist, dag-architect, risk-analyst, verification-planner). Each is a standard Claude Code agent definition. |
| `roster.md` | The tag-first index of the 7 sub-agents (read this to *choose* one; read the body only after). |
| `schemas/tasks.schema.json` | The JSON Schema for the work graph (`tasks.json`) the Planner emits. |

## How to read it

1. Read **`PLANNER-ARCHITECTURE.md`** end to end — it's the explainer.
2. Then skim `skills/plan-mission/SKILL.md` + `GRILL.md` to see the actual driving prompt.
3. Then the seven `agents/*.md` to see how the heavy work is delegated and what each helper returns.

## How to run it (Claude Code)

The layout mirrors a Claude Code plugin, so the pieces are runnable:

- Drop `agents/*.md` into `.claude/agents/` and `skills/plan-mission/` into `.claude/skills/` (in a
  project, or under `~/.claude/` for global use).
- Invoke the front door with `/plan-mission <your goal>`.
- It will interview you, optionally dispatch the research sub-agents, reflect a "Mission Understanding"
  back for per-part sign-off, and then write a **Mission Blueprint** to `<target>/.plan/`
  (`mission.md`, `tasks.json`, `doc-manifest.md`, a lean `CLAUDE.md`, `verification-plan.md`).

> The Planner only *produces the plan*. Executing that plan (spinning up managers/workers to build it)
> is a separate system not included here — but the Blueprint is a clean, documented contract (see
> `schemas/tasks.schema.json`), so it's straightforward to point any executor at it.

## Notes on the decoupling

To make this stand alone, references to the parent system's internal components and decision-log IDs
were removed or generalized. Where a downstream "execution system" / "managers (orchestrators)" /
"workers" are mentioned, those are just the consumers of the Blueprint — the Planner doesn't depend on
their internals. Provenance kept intact: the interview protocol (`GRILL.md`) is adapted from Matt
Pocock's `grill-me` / `grill-with-docs` skills (https://github.com/mattpocock/skills).
