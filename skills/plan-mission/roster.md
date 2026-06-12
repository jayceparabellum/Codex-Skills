# Planner sub-agents — the tag-first index

> The Planner picks a sub-agent by reading **this index of one-line tags**, not the full agent bodies
> (progressive disclosure). Each agent's full definition is in `agents/<name>.md` and loads only into the
> spawned sub-agent. The `dag-architect` selects the **minimal sufficient** set per mission.

| Agent | `when` (the routing tag) | model | parallel |
|---|---|---|---|
| **audience-analyst** | figure out WHO it's for + what they want (recipient/company mission/values, where to stand out) | sonnet | yes |
| **requirements-elicitor** | turn a vague goal into structured, testable requirements + open questions | sonnet | no |
| **scope-researcher** | heavy research: domain, OSS/GitHub feasibility, tool match, brownfield codebase map | sonnet | yes |
| **creative-strategist** | brainstorm how to make it stand out / better — the one memorable idea + ranked creative options (opt-in) | sonnet | yes |
| **dag-architect** | decompose agreed scope into the work graph + select the minimal roster | sonnet | no |
| **risk-analyst** | surface initial problems, irreversible steps, required human checkpoints | sonnet | yes |
| **verification-planner** | define the real-artifact proof per done-criterion | sonnet | yes |

**Reading rule:** match the work's need to a `when` tag here; spawn that `subagent_type`. Read the body
(`agents/<name>.md`) only inside the spawned sub-agent, never to choose.

**Note on the interview:** the relentless interview the Planner runs (the "Grill",
`skills/plan-mission/GRILL.md`) is **not** a sub-agent — it must talk to the user mid-run, and sub-agents
in this model can't converse with the user. So it lives as a skill protocol in the main conversation, while
everything heads-down (research, decomposition, risk, verification) is delegated to the sub-agents above.

---

> **Beyond the Planner:** `dag-architect` also assigns each task an `owner_role` drawn from a *worker*
> roster (implementer, reviewer, verifier, etc.) belonging to the downstream execution system. That worker
> roster is not part of this package — define it for whatever executor consumes the Blueprint, and have
> `dag-architect` pick from it.
