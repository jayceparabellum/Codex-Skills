---
name: prd
description: "Scaffold a new Product Requirements Document (PRD) in docs$prds/ by interrogating the user about the feature, success criteria, and expected services. Use when the user asks to \"write a PRD\", \"scaffold a PRD\", \"draft a PRD for X\", or invokes $prd. Interrogates first, then writes \" does not guess answers from context."
---

# Scaffold a new PRD

Interrogates the user, then writes a PRD file to `docs$prds/`. The interrogation is the point " do not skip it, do not infer answers from prior conversation.

## Step 1: Pick the next number

PRDs are numbered with a 4-digit zero-padded prefix (`0001-`, `0002-`, ). Determine the next number:

```bash
ls docs$prds/ 2>/dev/null | grep -E '^[0-9]{4}-' | sort | tail -1
```

Next number = highest existing + 1, formatted `%04d`. If `docs$prds/` does not exist, start at `0001`.

Empty placeholder files (size 0) count as taken " do not overwrite them. If the user wants to fill an existing empty placeholder, they will say so explicitly; otherwise pick the next number after it.

## Step 2: Interrogate the user

Ask three rounds of questions using `AskUserQuestion`. Do **not** batch all three rounds into one call " each round's answers shape the next round's framing. Do **not** invent answers from session context, even if the topic was discussed earlier; the user invoked this skill because they want to think through the PRD explicitly.

Each round below lists the *minimum* questions. Add follow-ups when an answer is vague (e.g. user says "make it faster" " ask "faster than what, measured how?").

### Round 1 " What is the feature?

- **Working title.** A short name. Used in the filename slug.
- **One-sentence summary.** What does this feature do, in plain language?
- **Problem it solves.** What specifically is broken / missing / painful today? Who feels it?
- **Out of scope.** What might a reader assume is included that is *not*?

### Round 2 " Success criteria

- **How do we know it works?** Concrete, observable signals " not "users are happy". Prefer signals that could be checked in a test, a metric, or a demo.
- **How do we know it's done?** The minimum cut that ships. What's deferred to a follow-up?
- **Non-goals.** What outcomes are explicitly *not* success for this PRD?

### Round 3 " Services and components

- **New services.** What new services / classes / modules do you expect to create? (Names if you have them, rough responsibilities if you don't.)
- **Existing code touched.** What existing services, controllers, tables, or templates will this modify or extend?
- **External dependencies.** Any new packages, APIs, infrastructure, or third-party services?
- **Data model changes.** New tables, columns, migrations, or schema shifts?

If the user pushes back ("I don't know yet, that's why I'm writing the PRD"), accept that and record the question itself in the PRD's **Open questions** section rather than forcing an answer.

## Step 3: Derive the slug

From the working title, produce a kebab-case slug: lowercase, alphanumeric + hyphens, no leading/trailing hyphens, no consecutive hyphens. Trim to ~50 chars at a word boundary.

Filename: `docs$prds/<NNNN>-<slug>.md`

## Step 4: Write the PRD

Write the file using this template. Fill every section from the user's answers " do not invent content. Sections with no answer get an explicit `_TBD " see Open questions._` line, not silence.

```markdown
# PRD <NNNN>: <Working Title>

- **Status:** Draft
- **Author:** <git user.name from `git config user.name`>
- **Created:** <today's date, YYYY-MM-DD>

## Summary

<one-sentence summary>

## Problem

<problem it solves; who feels it>

## Goals / Success Criteria

<how we know it works " observable signals>

## Non-goals

<what is explicitly not success>

## Scope

### In scope
<minimum cut that ships>

### Out of scope
<things a reader might assume but are not included>

## Proposed Design

### New services / components
<new services / classes / modules>

### Existing code touched
<existing services / controllers / tables / templates>

### Data model changes
<schema / migrations>

### External dependencies
<packages / APIs / infra>

## Open questions

<questions the user could not answer; one per line as a bullet>

## References

<links, tickets, prior PRDs " leave empty if none provided>
```

Use the Write tool. Don't ask the user to confirm contents before writing " they can edit afterward. The PRD is a draft, not a contract.

## Step 5: Report

One line: path of the new file with the heading line number (`docs$prds/NNNN-slug.md:1`), and the next suggested action (e.g. "edit to refine before circulating", "open a tracking ticket linking to it"). Stop.

## Hard rules

- **Always interrogate.** Even if the user has been talking about the feature for an hour, ask the three rounds explicitly. The act of answering structured questions is what produces a useful PRD.
- **Never guess answers** from earlier conversation. If the user already said something relevant, you may *quote it back* in the question ("earlier you said X " is that still the goal, or has it shifted?") but do not write it into the PRD without confirmation.
- **One PRD per invocation.** Don't bundle multiple features into one file. If the user describes two things, ask which one this PRD covers.
- **Don't create tickets.** This skill writes a markdown file. Filing a tracking ticket (GitHub/GitLab/etc.) is a separate step the user initiates.
- **Don't number-collide.** Re-check the next number right before writing in case files were added during interrogation.
