---
name: pre-search-checklist
description: "Walk the user through a structured pre-search checklist before they start building an AI agent or LLM-powered application. Use BEFORE any code is written \" covers domain constraints, architecture discovery, and post-stack refinement so the user has answered the load-bearing questions about scope, reliability, framework choice, evals, verification, security, and ops. TRIGGER when the user is starting a new agent/LLM project, says they're \"scoping\" or \"planning\" an agent, asks \"what should I think about before building X\", mentions a presearch / pre-search phase, or is about to choose between agent frameworks (LangChain / LangGraph / CrewAI / custom) or LLM providers. SKIP for projects already past planning, for non-agent code work, or when the user just wants to start coding."
---

# Pre-Search Checklist

## Overview

This skill walks the user through a 16-section checklist used to define constraints and architecture **before** writing code for an AI agent or LLM-powered system. The output is a saved reference document the user can hand to future-you (or a teammate) to ground later implementation decisions.

The goal is **alignment, not interrogation.** Move through the checklist conversationally, batching related questions, and skipping or compressing sections that don't apply (e.g., a personal weekend project doesn't need a full deployment + open-source plan). Save answers as you go.

## How to run this skill

1. **Confirm scope first.** Ask the user one or two short questions to learn what they're building and how formal this needs to be:
   - "What are you building, in one sentence?"
   - "Is this a personal/prototype project or something headed toward production?"

   Use the answers to decide depth. For a prototype, treat Phases 1"2 as required and Phase 3 as a quick sanity pass. For anything production-bound, walk all three phases.

2. **Work through the phases below.** For each section, ask the bulleted questions as a small group (not one-by-one), summarize the user's answer back in your own words, and capture it. Don't move to the next section until the current one has a real answer or an explicit "skip " N/A".

3. **Save the output.** When the checklist is done (or the user calls it), write the answers to a single Markdown file in the user's current working directory: `presearch.md` (or a name the user prefers). Structure it with the same section headings as the checklist so it's easy to scan.

4. **Flag gaps and contradictions.** If the user's answers in one section conflict with another (e.g., "low latency required" + "use a 200B open-source model on a single GPU"), surface the tension before moving on.

---

## Phase 1: Define Your Constraints

### 1. Domain Selection
- Which domain: healthcare, insurance, finance, legal, or custom?
- What specific use cases will you support?
- What are the verification requirements for this domain?
- What data sources will you need access to?

### 2. Scale & Performance
- Expected query volume?
- Acceptable latency for responses?
- Concurrent user requirements?
- Cost constraints for LLM calls?

### 3. Reliability Requirements
- What's the cost of a wrong answer in your domain?
- What verification is non-negotiable?
- Human-in-the-loop requirements?
- Audit/compliance needs?

### 4. Team & Skill Constraints
- Familiarity with agent frameworks?
- Experience with your chosen domain?
- Comfort with eval/testing frameworks?

---

## Phase 2: Architecture Discovery

### 5. Agent Framework Selection
- LangChain vs LangGraph vs CrewAI vs custom?
- Single agent or multi-agent architecture?
- State management requirements?
- Tool integration complexity?

### 6. LLM Selection
- GPT-5 vs Claude vs open source?
- Function calling support requirements?
- Context window needs?
- Cost per query acceptable?

### 7. Tool Design
- What tools does your agent need?
- External API dependencies?
- Mock vs real data for development?
- Error handling per tool?

### 8. Observability Strategy
- LangSmith vs Braintrust vs other?
- What metrics matter most?
- Real-time monitoring needs?
- Cost tracking requirements?

### 9. Eval Approach
- How will you measure correctness?
- Ground truth data sources?
- Automated vs human evaluation?
- CI integration for eval runs?

### 10. Verification Design
- What claims must be verified?
- Fact-checking data sources?
- Confidence thresholds?
- Escalation triggers?

---

## Phase 3: Post-Stack Refinement

### 11. Failure Mode Analysis
- What happens when tools fail?
- How to handle ambiguous queries?
- Rate limiting and fallback strategies?
- Graceful degradation approach?

### 12. Security Considerations
- Prompt injection prevention?
- Data leakage risks?
- API key management?
- Audit logging requirements?

### 13. Testing Strategy
- Unit tests for tools?
- Integration tests for agent flows?
- Adversarial testing approach?
- Regression testing setup?

### 14. Open Source Planning
- What will you release?
- Licensing considerations?
- Documentation requirements?
- Community engagement plan?

### 15. Deployment & Operations
- Hosting approach?
- CI/CD for agent updates?
- Monitoring and alerting?
- Rollback strategy?

### 16. Iteration Planning
- How will you collect user feedback?
- Eval-driven improvement cycle?
- Feature prioritization approach?
- Long-term maintenance plan?

---

## Output format

When saving the checklist results, use this structure so the file is useful as a reference doc later:

```markdown
# Presearch " <project name>

_Date: <YYYY-MM-DD>_
_Stage: <prototype | pilot | production>_

## Phase 1: Constraints
### 1. Domain Selection
- Domain: ...
- Use cases: ...
- Verification requirements: ...
- Data sources: ...

[...repeat for each section...]

## Open questions / unresolved
- ...

## Decisions locked in
- ...
```

The "Open questions" and "Decisions locked in" sections at the end are the most valuable part " they tell the user (and future contributors) what's still soft and what's already committed.

## When to recommend the `scaffold` skill next

Once the checklist is filled in, the natural next step is to generate project documentation from it. Tell the user they can run `$scaffold` to turn the presearch findings into `PRD.md`, `TECH_STACK.md`, and other project docs.
