# The Planner — a deep-alignment planning agent (architecture)

> A **front door** and an **alignment gate** for an autonomous build system. You bring a project or
> goal; the Planner works back and forth with you until you're **perfectly aligned**, proves it
> understands by reflecting the project back to you, and only then emits a structured **Mission
> Blueprint** that a downstream execution system can build unattended.
>
> This document is self-contained. It was extracted from a larger autonomous-engineering system, but
> nothing here depends on the rest of that system — the Planner is the piece that turns a fuzzy human
> intent into a frozen, machine-consumable plan, and that job stands on its own.

---

## 1. Why the Planner exists & its principles

The single biggest lever on "the build doesn't go sideways" is **starting from a correct, complete,
agreed plan.** Garbage in, garbage out — so the Planner spends real effort here, before any building.

Five principles:

1. **Alignment is a hard gate.** Nothing reaches the execution system until you've signed off. A "just
   start building" instruction *cannot* skip it — it's a non-overridable checkpoint.
2. **Comprehension before construction.** The Planner must *prove it understands* by reflecting the
   project back to you ("here's where I believe we are") — that reflection is your test that it's safe
   to proceed.
3. **Front-load the expensive stuff.** The **majority of research** and the **human setup**
   (credentials, tool installs, irreversible-action approvals) happen here, so the build runs
   unattended instead of stalling halfway to ask you a question.
4. **Practice progressive disclosure.** The Planner delegates heavy reading/research to sub-agents that
   return short briefs, keeping the planning conversation itself lean.
5. **Output a contract, not prose.** The Blueprint is structured and machine-consumable — it's what
   every downstream step reads.

It is the **one place heavy human collaboration is wanted** — everywhere downstream trends autonomous;
here, getting it right *with you* matters more than speed. Expect a lot of back-and-forth. That's
correct, not a failure.

---

## 2. The flow

The Planner runs as a single interactive session (in this implementation, a user-invoked skill called
`/plan-mission`). It moves through six phases:

```
  /plan-mission   (you run this; you bring the goal + optionally a repo/files)
        │
  ┌─────▼──────────────────────────────────────────────────────────────────────┐
  │ PHASE 0 — INTAKE & CLASSIFY                                                  │
  │   read the goal + any linked repo/files · greenfield or brownfield? · domain │
  │   · rough size · if BROWNFIELD → scope-researcher ingests + maps the codebase│
  └─────┬──────────────────────────────────────────────────────────────────────┘
        ▼
  ┌────────────────────────────────────────────────────────────────────────────┐
  │ PHASE 0.5 — WHO IS THIS FOR  (gates everything below)                        │
  │   audience-analyst researches the recipient/evaluator/company; then GRILL the │
  │   user to confirm. A deliverable that ignores its audience misses.            │
  └─────┬──────────────────────────────────────────────────────────────────────┘
        ▼
  ┌────────────────────────────────────────────────────────────────────────────┐
  │ PHASE 1 — GRILL, PROPOSE RESEARCH, THEN ELICIT  (the heavy phase)            │
  │   Grill the user one question at a time (each WITH a recommended answer);     │
  │   present "here's what I propose to research and why" → get approval BEFORE    │
  │   deploying research agents; then requirements-elicitor (+ scope-researcher). │
  └─────┬──────────────────────────────────────────────────────────────────────┘
        ▼
  ┌────────────────────────────────────────────────────────────────────────────┐
  │ PHASE 1.5 — CREATIVITY & OPTIONALITY  (always ask)                           │
  │   "Where do you want me creative? Should I improve beyond the literal ask?"   │
  │   If opted in → creative-strategist brainstorms the standout ideas.           │
  └─────┬──────────────────────────────────────────────────────────────────────┘
        ▼   (once scope is roughly agreed, fan out in parallel:)
  ┌────────────────────────────────────────────────────────────────────────────┐
  │ PHASE 2 — SYNTHESIZE THE "MISSION UNDERSTANDING"  ("here's where I believe   │
  │   we are")  ── built from:                                                   │
  │     dag-architect        → the journey (work graph) + minimal roster          │
  │     risk-analyst         → initial problems / risks / required checkpoints     │
  │     verification-planner → how each done-criterion gets proven on the real app │
  └─────┬──────────────────────────────────────────────────────────────────────┘
        ▼
  ┌────────────────────────────────────────────────────────────────────────────┐
  │ PHASE 3 — PER-PART CRITIQUE LOOP  (the alignment gate)                       │
  │   present each part → you critique → revise → repeat until you sign off on    │
  │   EVERY part. Cannot proceed otherwise. "Just build it" does not skip this.   │
  └─────┬──────────────────────────────────────────────────────────────────────┘
        ▼
  ┌────────────────────────────────────────────────────────────────────────────┐
  │ PHASE 4 — RESOLVE HUMAN SETUP  (front-load)                                  │
  │   any tool/MCP needing a credential or install, any irreversible-action      │
  │   approval, any deployment → ask NOW, with BEGINNER step-by-step instructions.│
  └─────┬──────────────────────────────────────────────────────────────────────┘
        ▼
  ┌────────────────────────────────────────────────────────────────────────────┐
  │ PHASE 5 — EMIT THE MISSION BLUEPRINT  (the skill writes the files)           │
  │   <target>/.plan/{mission.md, tasks.json, doc-manifest.md, CLAUDE.md (lean), │
  │     verification-plan.md}                                                     │
  │   final sign-off → "the execution system can now build this."                 │
  └────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. The Grill — the relentless-interview protocol

The Planner reaches alignment by **interviewing**, not by dumping a form. This protocol runs *in the
conversation* (it can't be delegated to a sub-agent — sub-agents can't talk to the user mid-run). It's
adapted from Matt Pocock's `grill-me` / `grill-with-docs` skills (https://github.com/mattpocock/skills).

Seven non-negotiable rules:

1. **Interview relentlessly** about every aspect of the plan until you and the user reach genuine shared
   understanding. Walk down **each branch of the decision tree**, resolving dependencies one at a time.
2. **Ask one question at a time.** Wait for the answer before the next. Never dump a wall of questions.
3. **Always provide your recommended answer** with each question, and *why* — the user should be able to
   just say "yes" to a good default.
4. **Explore before you ask.** If a question can be answered by reading the brief, the repo, or by
   research, do that instead. Never ask what you can find out.
5. **Sharpen fuzzy language.** When the user says something vague or overloaded, propose the precise
   term and confirm. ("You said 'users' — do you mean the 8-year-old player, or the hiring partner
   evaluating this?")
6. **Stress-test with concrete scenarios.** Invent specific cases that probe the edges and force
   precision.
7. **Resolve dependencies in order.** Decisions that gate other decisions come first: *who is this for*
   gates *how creative*, which gates *what to research*, which gates *the work graph*.

The decision tree the Planner walks: **who is this for** → **what it really is & the goal behind the
goal** → **creativity & optionality** → **the research plan** → **scope ceiling, end-state, risks,
verification, autonomy**.

---

## 4. The "Mission Understanding" — the comprehension checkpoint

This is the artifact the Planner reflects back before building. It is presented as a readable narrative
(beginner-friendly) backed by the structured drafts the sub-agents produced. It has **nine parts, each
separately critique-able** — Phase 3 loops over them one at a time:

1. **What it is** — the project in plain language, one paragraph.
2. **Who it's for** — the audience/recipient and where the standout opportunity is (from
   `audience-analyst`). *This gates everything else.*
3. **Goals & end-state** — concrete, testable done-criteria; *what it looks like when finished.*
4. **Initial problems** — the known hard parts, unknowns, risky/irreversible steps (from `risk-analyst`).
5. **The journey** — *every step the build will take*: which managers own what, and the shape of the
   work graph (from `dag-architect`). This is the part users most often want spelled out explicitly.
6. **Approach & tools** — stack decisions + chosen tools/MCPs + the **minimal roster** of worker roles
   for this mission.
7. **How we'll prove it** — the real-artifact verification per done-criterion (from
   `verification-planner`). For brownfield work, the "existing tests stay green + real check" bar.
8. **Creativity & optionality (chosen)** — which creative directions the user opted into (from
   `creative-strategist`), if any.
9. **Autonomy mode + what I need from you** — how hands-on this mission runs (hands-off / surface-on-
   blocker / milestone check-ins / live progress), plus the open questions and the human-setup list.

You critique part by part; the Planner revises; you sign off. Only a fully-signed-off Understanding
becomes a Blueprint.

---

## 5. The seven planner sub-agents

Each is a normal agent definition (`agents/<name>.md`) with a tight one-line `description` (the routing
tag), restricted tools, and a short return contract (≤200–280 words). They keep the planning
conversation lean by doing heavy work in their own context and returning **briefs** — the full detail is
written to draft files, not into the planning window.

| Sub-agent | When to use it | Tools | Returns |
|---|---|---|---|
| **`audience-analyst`** | figure out WHO the deliverable is really for + what they actually want (recipient/company mission/values, where to stand out) | Read, Grep, Glob, WebSearch, WebFetch | the recipient · their goal/values · what "good" means *to them* · where the standout opportunity is · concrete implications for the build |
| **`requirements-elicitor`** | turn a vague goal into structured, testable requirements + the questions still open | Read, Grep, Glob | requirements brief (functional / non-functional / constraints), each phrased testably + a ranked list of open questions |
| **`scope-researcher`** ⭐ | heavy front-loaded research — domain, existing OSS/GitHub solutions, the right tools/MCPs, and (brownfield) a map of the existing codebase | Read, Grep, Glob, Bash, WebSearch, WebFetch | findings brief: recommended approach + stack + tools (with *why*), feasibility risks, and (brownfield) a codebase map (architecture, stack, test health, conventions, risky areas) |
| **`creative-strategist`** | brainstorm how to make the deliverable stand out, not just be correct (opt-in) | Read, Grep, Glob, WebSearch, WebFetch | the *one memorable idea* · 3–6 ranked creative directions (idea / why / effort S-M-L / risk) · the anti-gold-plating notes |
| **`dag-architect`** | decompose agreed scope into the work graph (right-sized, file-partitioned tasks with deps) + pick the minimal roster | Read, Grep, Glob | the `tasks.json` draft (validates against the schema), the chosen roster, and the orchestrator grouping |
| **`risk-analyst`** | surface initial problems, unknowns, irreversible/risky steps, and required human checkpoints | Read, Grep, Glob | a ranked risk register + the irreversible-step list + the human-checkpoint list (what to ask, when) |
| **`verification-planner`** | define how each done-criterion gets proven on a *real artifact* | Read, Grep, Glob | per criterion: what to run / what to watch / the pass signal / witness markers; the brownfield baseline note |

`scope-researcher` carries the bulk of the research and scans new OSS repos for feasibility. After Phase
1 agrees the scope, `dag-architect` / `risk-analyst` / `verification-planner` **fan out in parallel**
(they're independent and each returns a summary) — the Planner orchestrating its own helpers the same way
the downstream build system does.

> **A key constraint that shapes this:** the interactive interview (the Grill) lives as a *skill
> protocol in the conversation*, not as a sub-agent — because sub-agents in this model can't converse
> with the user mid-run. Everything that must talk to the user stays in the main session; everything
> that's heads-down research is delegated to a sub-agent that returns a brief.

---

## 6. The Mission Blueprint — the output contract

When every part is signed off, the skill writes the Blueprint to `<target>/.plan/`:

| File | Contents |
|---|---|
| **`mission.md`** | what it is · who it's for · goals + end-state · initial problems · the journey narrative · chosen approach + creativity choices + autonomy mode. The human-readable contract. |
| **`tasks.json`** | the work graph (DAG) — the single source of truth for work. Each task: `{id, goal, inputs, file_whitelist, success_criteria, deps[], owner_role, state}`. Right-sized (a function/test/review unit) and **file-partitioned** so parallel workers get clean, non-colliding worktrees. Validates against `schemas/tasks.schema.json`. |
| **`doc-manifest.md`** | tag-first pointers to the corpus (incl. any brownfield codebase map) — read on demand. |
| **`CLAUDE.md`** (lean) | the standing contract handed identically to every manager — golden rules, roster, where state lives, invariants. Kept small (it loads into every agent). |
| **`verification-plan.md`** | per done-criterion: the real-artifact proof. The acceptance bar. |

This package is the **hard gate**: the execution system does not start until it exists and you've signed
off. Once emitted, the Blueprint is **frozen** for the duration of the build — a change to scope means a
re-plan, not an in-flight edit. (The `tasks.json` schema marks this with `"frozen": true`.)

---

## 7. Greenfield vs brownfield

- **Greenfield:** run the planner in a new/empty directory; Phase 0 skips codebase ingestion; the work
  graph builds the thing from scratch; verification proves each new behavior on the real artifact.
- **Brownfield:** Phase 0 dispatches `scope-researcher` to **map the existing codebase first**; the work
  graph is **strictly scoped** to what you asked (per-task file-whitelists enforce it); out-of-scope
  issues are **logged as suggestions**, not changed; verification adds the **"all pre-existing tests
  stay green"** bar and pauses on a pre-existing failure.

---

## 8. Two ideas that make it efficient

**Progressive disclosure (the "tag" trick).** A manager shouldn't read a worker's entire job description
just to decide whether to use it — that wastes context. So everything is accessed in three layers:

```
   TAG    one line: "what this is / when to use it"   ← read this to CHOOSE
    │
   INDEX  a tiny list of all the tags                 ← search this to FIND
    │
   BODY   the full thing                              ← open only AFTER you've chosen
```

This is also how Claude Code natively works — a sub-agent's `description` is the tag the model reads to
decide whether to route to it — so short tags aren't just our style, they're mechanically required. The
planner sub-agents' `description:` lines (and the `roster.md` index) are exactly these tags.

**Front-loading.** Research and human setup are pulled forward into planning on purpose, so the build
phase never stops to ask you for a credential or to go read a library's docs. The cost of a question is
much lower *before* the build starts than mid-flight.

---

## 9. Build notes (for re-implementing this)

- The planner is a **user-invocable skill** — the user deliberately starts a project with it (model-
  invocation is disabled so it never auto-fires; its description still lists so the model knows it
  exists).
- The seven sub-agents live in `agents/` as standard agent definitions with restricted tools.
- Heavy reads should use a forked context / a read-only explore agent so bulk corpus never enters the
  planning window.
- The critique loop uses structured choice prompts where the answer is a pick, and free-form where it's
  open — and it **must not** skip a part's sign-off even under a "work without stopping" instruction.
- Every significant decision should be **data-backed** (research/evidence or a measured rationale), not
  vibes.
- "Green tests" is never accepted as proof of done — the verification plan always requires *watched
  real-artifact evidence* (run the actual app, observe the actual behavior).

---

## 10. The files in this package

```
PLANNER-ARCHITECTURE.md          ← you are here (the standalone explainer)
README.md                        ← how to read / run / adapt this package
skills/plan-mission/
  SKILL.md                       ← the front-door skill (the 6-phase flow + hard rules)
  GRILL.md                       ← the relentless-interview protocol
agents/
  audience-analyst.md            ← the 7 planner sub-agents, each with its routing tag,
  requirements-elicitor.md          restricted tools, and ≤200–280-word return contract
  scope-researcher.md
  creative-strategist.md
  dag-architect.md
  risk-analyst.md
  verification-planner.md
roster.md                        ← the tag-first index of the 7 sub-agents
schemas/
  tasks.schema.json              ← the output contract for the work graph (tasks.json)
```
