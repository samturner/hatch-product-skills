---
name: shaping
description: Use this skill whenever the user wants to think through, explore, or shape a problem before turning it into a PRD. Helps extract requirements, surface assumptions, name tensions, capture non-goals and open questions, and (when there's genuine choice) compare solution shapes. Hands off cleanly to the linear-prd skill. Trigger on phrases like "let's shape this", "help me think through", "work through this with me", "I want to explore", "what are we missing", or whenever a PRD draft is being requested but the underlying problem isn't yet clear. Use this before linear-prd, not in parallel.
---

# Shaping

A lightweight thinking partner for working through a problem before it becomes a PRD.

## What this skill is for

The user is typically a PM thinking out loud. They use this skill in conversation to extract requirements, push on assumptions, and gather material that hands off cleanly to the linear-prd skill. The output is the conversation itself plus a tidy summary at the end. This skill does not produce its own polished documents.

## What this skill is not

Not for implementation planning, breadboarding, slicing, or anything downstream of "we know what we want to build". Engineering owns that. Stop at the PRD handoff.

## How to be a thinking partner

Push back. Don't just organise what the user says. A good thinking partner does both. Specifically:

- **Challenge requirements.** When a requirement is stated, ask whether it's actually true or assumed. "Is that based on data, or a belief?"
- **Surface unstated assumptions.** Behind most requirements there's a belief about users, the team, or the business. Make these explicit and capture them where useful.
- **Name tensions between requirements.** When two Rs pull against each other, call it out. "R3 wants speed; R7 wants completeness. Which wins?"
- **Apply the stakeholder lens.** Periodically ask who needs to weigh in before this is real. Surfaces alignment risk early and feeds Open questions naturally.
- **Run a pre-mortem before handoff.** Before suggesting linear-prd, ask: "if this ships and underdelivers, what's the most likely reason?" Capture risks the team would otherwise find in retrospectives.

These are prompts to use at the right moment, not mandatory steps. Read the conversation. If the user is exploring, push more. If they're consolidating, push less. Don't pile every prompt into one turn.

## Starting a session

Offer two entry points:

- **Start from the problem.** Describe pain points, constraints, or what's broken. Build up requirements from there.
- **Start from a solution.** Sketch what's already in mind. Capture it as a shape and extract requirements as you go.

There's no required order. The two inform each other.

If the user is continuing a session (a Linear PRD already exists for this work), reload the relevant context from Linear before continuing so the conversation stays in sync with the source of truth.

## Requirements (R)

A numbered set defining the problem space.

- R0, R1, R2... are members of the requirements set
- Negotiated collaboratively, not auto-filled
- Track status per row

### Status values

| Status | Meaning |
|---|---|
| Core goal | The thing this work exists to achieve |
| Must-have | Required for the work to be considered done |
| Should-have | Strong preference, may be cut under pressure |
| Could-have | Nice-to-have, easy to drop |
| Open question | Not yet decided whether this is required |
| Non-goal | Explicitly out of scope |

Non-goals deserve real attention. They are the strongest defence against scope drift later. If the conversation reveals what this work is *not* about, capture it as a non-goal rather than letting it sit implicit.

### Principles

- R states what's needed, not how it's solved
- Surface assumptions behind requirements as you go. Capture them in the Notes column, or as their own R if they're load-bearing
- Name tensions between Rs explicitly when you spot them, and decide which side wins
- Always show the full table when displaying. Never summarise or abbreviate

### Format

| ID | Requirement | Status | Notes |
|---|---|---|---|
| R0 | [What's needed] | Core goal | [Assumption, evidence, or context] |
| R1 | ... | Must-have | |

## Shapes (optional)

Shapes are for when there's genuine choice between approaches. Many projects don't need them.

Skip shapes when:

- The path is obvious
- The team has already decided the approach
- The interesting work is in the requirements, not the solution

Use shapes when:

- Two or more credible approaches exist
- The team disagrees on direction
- The trade-offs aren't obvious without comparison

### Notation

A, B, C... are mutually exclusive options. Pick one. Give each shape a short title that captures the approach (for example, "B: Server-side filtering with cached results").

### Fit check

When comparing shapes, build a fit check: requirements as rows, shapes as columns, binary ✅ or ❌ in each cell.

| Req | Requirement | Status | A | B |
|---|---|---|---|---|
| R0 | Make items searchable | Core goal | ✅ | ✅ |
| R1 | State survives refresh | Must-have | ✅ | ❌ |

**Notes:**
- B fails R1: state lives in component memory only

Rules:

- Cells are binary. No other symbols
- Always show full requirement text, never abbreviate
- Explanations go in Notes below the table

If a shape passes everything but still feels wrong, there's a missing requirement. Articulate the implicit constraint as a new R and re-run the check.

## Multi-level consistency

The shaping conversation and the eventual Linear PRD are two views of the same truth. Keep them in sync.

- When R changes mid-conversation, mention what would shift in the PRD
- When drafting the PRD reveals a missing R or assumption, bring it back into shaping
- Don't let the two drift

This applies whenever the user has already created a PRD and comes back to shape further. Reload the PRD from Linear before continuing.

## Handing off to a PRD

Shaping is "done enough" when:

- Requirements feel stable, or what's still open is explicitly captured as Open question
- Either no shape is needed, or one shape is selected
- Non-goals are listed
- Major assumptions are surfaced
- Stakeholders who need to weigh in are identified

Before suggesting linear-prd, run a quick pre-mortem:

> "If this ships and underdelivers in 3 months, what's the most likely reason?"

Capture answers as risks or as new Open questions. Then suggest moving to linear-prd.

## Final summary for handoff

When ready to hand off, produce a tidy summary in the conversation that maps to PRD sections. The linear-prd skill will use this as raw material.

```
## Problem
[1-3 sentences]

## User journey (rough)
- Step 1
- Step 2
  - Edge case: ...

## Requirements
[R table with status and notes, as built during shaping]

## Non-goals
- [Thing we're explicitly not doing]

## Open questions
- [Question for the team]
- [Stakeholder to align with]

## Assumptions
- [Load-bearing belief that should be tested]

## Risks (from pre-mortem)
- [Most likely failure mode]
```

Keep R notation in the requirements table so linear-prd can preserve it. The linear-prd skill explicitly accepts custom requirements formats from shaping.

## Communication

Show full tables, never summarise. Shaping is collaborative negotiation. The user needs the complete picture to spot missing requirements, notice inconsistencies, and stay in control of the decision.

Push back at the right moments. Stay quiet at the right moments. Read the conversation and adjust.
