---
name: prd
description: Create a PRD (Product Requirements Document) as a new project in Linear, using a standard section structure (Problem, User journey, Requirements, Design reference, Open questions). Use this skill whenever the user asks to create, draft, or spin up a PRD, turn existing thinking into a PRD, write a Linear project for a feature or initiative, or produce a product brief. Trigger even when the user doesn't explicitly say "Linear", since Linear is the destination by default. Also trigger on phrases like "let's turn this into a project", "write this up as a PRD", or "put this in Linear" when the preceding conversation has been shaping a product idea.
---

# Linear PRD

Creates a new project in Linear's **Product Eng & ML** team, populated with a standard PRD structure. The typical use case is that the user has been shaping a product idea in conversation and wants it written up as a PRD that lives in Linear alongside the team's work.

## Workflow

### 1. Gather context from the conversation

Before asking the user anything, scan back through the conversation for content that maps to each PRD section. Most of the time the conversation already contains useful raw material, because the user has been thinking out loud, aligning with collaborators, or quoting messages from the team.

Map what you find to these sections:

- **Problem**: any framing of what's broken, missing, or worth doing, and why now
- **User journey**: steps, flows, or user actions the conversation has touched on
- **Requirements**: specific things the team has aligned on (look for "we need X", "it should do Y", or confirmation language from stakeholders)
- **Design reference**: any mention of Figma, mocks, or prototypes
- **Open questions**: anything flagged as unresolved, TBD, or "need to check with someone"

If a section has nothing useful in context, mark it as a gap.

### 2. Show a draft before creating

Share a draft preview with the user containing:

- Proposed project title (the feature or initiative name, not a sentence). For example, "Recommendations v2 experimentation", not "Create a PRD for recs v2".
- What you pulled from context for each section
- What's missing, and whether they want to fill it now or leave a placeholder

Wait for confirmation before creating. PRDs in Linear become a source of truth for the team, so it's cheaper to confirm the framing now than to fix a misaligned PRD later.

If the project name isn't obvious from context, propose 2 or 3 options rather than guessing.

### 3. Find the team

The Linear team is always **Product Eng & ML**. List the Linear teams, match the name (case-insensitive), and use that team ID.

If the team can't be found, stop and ask the user which team to use. Don't silently pick a fallback.

### 4. Find the Shaping project status

The project must always be created in the **Shaping** state. Linear projects have a status field that points to a named project status (Backlog, Planned, In Progress, etc., plus any custom statuses the workspace has added). "Shaping" is a custom status used by this team.

Look up the available project statuses for the Product Eng & ML team and find the one named "Shaping" (case-insensitive). Use that status ID when creating the project.

If no "Shaping" status exists, stop and ask the user how to proceed. Don't fall back to Backlog or Planned silently, because the team relies on the Shaping state to filter work in progress.

### 5. Create the project

Create the Linear project with:

- Name: the confirmed title
- Team: the Product Eng & ML team ID
- Status: the Shaping status ID
- Description / body: the markdown template below, filled in

After creation, share the project URL back to the user. Keep the confirmation short. Don't re-summarise the PRD contents, because the user can click through.

## PRD body template

Use this structure for the project body. Keep it clean, because the team will read it many times.

~~~markdown
## Problem

[1 to 3 sentences describing what we're solving and why it matters now. If unknown: _TBD: what problem does this solve?_]

## User journey

[End-to-end flow as bullet points. Use sub-bullets for branching logic or edge cases. Keep it scannable. Dot points, not prose.]

- Step 1
- Step 2
  - Branch: if X, then ...
  - Branch: if Y, then ...
- Step 3

## Requirements

| Requirement | Priority | Notes |
| --- | --- | --- |
| [aligned requirement] | Must / Should / Could | [context] |

[If no requirements are aligned yet: _TBD: to be filled in after shaping._]

## Design reference

_Paste Figma link here when ready. Linear will auto-embed it._

## Open questions

- [Question 1]
- [Question 2]
~~~

### Notes on each section

**Problem.** One short paragraph. This is the "why" that anchors the rest. Resist padding it out.

**User journey.** Dot points with sub-points for branching. If the flow is genuinely simple (2 or 3 steps), that's fine. Don't invent complexity.

**Requirements.** Only things the team has actually aligned on. Aspirations or ideas still being debated belong in Open questions. If the user has a different requirements format already established (for example from their shaping skill or a template in the conversation), use that format instead of the default table.

**Design reference.** Leave as placeholder text. The user pastes the Figma URL later and Linear handles the embed natively.

**Open questions.** Genuine unresolved items for the team to work through together. Not rhetorical prompts, and not things you could answer yourself.

## Writing style

The user writes in Australian English and dislikes obvious AI writing tells. Apply this to all generated PRD content (problem statements, user journey steps, requirements, questions).

Specifics:

- Use Australian spellings: organise, prioritise, behaviour, centre, recognise, etc.
- Don't use em dashes (—) or en dashes (–). Use a full stop, comma, colon, parentheses, or just restructure the sentence.
- Don't use ellipses (...) for stylistic effect.
- Avoid the "it's not just X, it's Y" construction.
- Avoid throat-clearing phrases like "let's dive in", "in the world of", "navigate the landscape", "delve into".
- Write plainly. Short sentences are fine. Contractions are fine. The goal is content that reads like the user wrote it themselves at their desk.

## What to avoid

- Don't create the project before the user has seen a draft and confirmed.
- Don't create the project in any state other than Shaping.
- Don't pad thin sections with filler to make them look substantial. A short honest section beats a bloated one.
- Don't invent requirements the team hasn't actually aligned on. If you're unsure whether something is aligned, put it in Open questions.
- Don't rewrite the whole PRD in the chat after creating it. The URL is enough.
