# The Grill — the Planner's relentless-interview protocol

> Adapted from Matt Pocock's `grill-me` / `grill-with-docs` skills
> (https://github.com/mattpocock/skills). The Planner runs this protocol IN THE CONVERSATION (it can't be a
> sub-agent — sub-agents can't talk to the user mid-run). Goal: reach genuine shared understanding before
> anything is built. The Planner is the most important step; this is how it earns that.

## The rules (non-negotiable)
1. **Interview relentlessly** about every aspect of the plan until you and the user reach shared
   understanding. Walk down **each branch of the decision tree**, resolving dependencies between decisions
   one at a time.
2. **Ask one question at a time.** Wait for the answer before the next. Do NOT dump a wall of questions.
   (Use structured choice prompts for picks; free-form for open ones.)
3. **Always provide your recommended answer** with each question, and *why*. The user should be able to just
   say "yes" to a good default.
4. **Explore before you ask.** If a question can be answered by reading the brief, the repo, or by research,
   do that instead of asking. Never ask what you can find out.
5. **Sharpen fuzzy language.** When the user says something vague or overloaded, propose the precise term and
   confirm. ("You said 'users' — do you mean the 8-year-old player or the hiring partner evaluating it?")
6. **Stress-test with concrete scenarios.** Invent specific cases that probe the edges and force precision.
7. **Resolve dependencies in order.** Decisions that gate other decisions come first (e.g. "who is this for"
   gates "how creative," which gates "what to research," which gates the work graph).

## The branches to walk (the planner's decision tree)
- **Who is this for** (audience-analyst) — the recipient, their mission/values, what "good" means to them,
  where the standout opportunity is. *This gates everything.*
- **What it really is & the goal behind the goal** — not just the literal ask; the outcome the user/recipient
  actually wants.
- **Creativity & optionality** — where should I be creative? Should I proactively improve? Should I deploy
  the creative-strategist to brainstorm standout ideas? (Always ask; **recommend bold** for
  competitive/case-study work.)
- **Research plan** — present "here's what I propose to research and why" and get approval BEFORE deploying
  research agents. Ask: is it smart to research X before building?
- **Scope ceiling, end-state, risks, verification, autonomy** — the rest of the Mission Understanding.

## When to stop
Stop grilling a branch when the user's answer is decisive and its dependents are unblocked. Stop the whole
grill when every part of the Mission Understanding is signed off (the alignment gate). A "just build it" does
NOT end the grill early — alignment is a hard gate.
