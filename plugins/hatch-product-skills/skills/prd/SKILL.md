---
name: prd
description: Create or update a PRD (Product Requirements Document) as a project in Linear, using the Hatch house-style section structure (Problem, User journey, Context, Requirements, Definition of done, Out of scope, Decisions, Design reference, Open questions). Use this skill whenever the user asks to create, draft, or spin up a PRD, turn existing thinking into a PRD, write a Linear project for a feature or initiative, produce a product brief, or update an existing PRD with new decisions from shaping. Trigger even when the user doesn't explicitly say "Linear", since Linear is the destination by default. Also trigger on phrases like "let's turn this into a project", "write this up as a PRD", or "put this in Linear" when the preceding conversation has been shaping a product idea.
---

# Linear PRD

Creates or updates a project in Linear's **Product Eng & ML** team, populated with the Hatch house-style PRD structure. The typical use case is that the user has been shaping a product idea in conversation and wants it written up as a PRD that lives in Linear alongside the team's work.

## Where things live

The pm-workspace (where shaping happens) is upstream of Linear. Keep each artifact in one place:

- **PRD body** → Linear project description. This skill writes here.
- **Resolved decisions with deeper reasoning** → numbered ADRs in the project's local `decisions/` folder (see CLAUDE.md in the pm-workspace). The PRD Decisions table carries a one-line reason and links to the ADR when one exists.
- **Issues and tasks** → Linear issues under this project. Don't duplicate them in the local repo.

## Workflow

### 1. Check for an existing PRD

Before gathering context, check whether a PRD already exists for this work. Search Linear projects in the Product Eng & ML team for a matching name or topic. This is especially likely when the user has been continuing a shaping session.

If a matching project exists, stop and ask the user whether to update it or create a new one. Updating is the default for continuing work. See "Updating an existing PRD" below for the update flow.

If nothing matches, proceed to Step 2.

### 2. Gather context from the conversation

Scan back through the conversation for content that maps to each PRD section.

If the conversation includes a handoff summary from the shaping skill (Problem, User journey, Context, Requirements with R IDs, Definition of done, Out of scope, Decisions, Design reference, Open questions), use that as the primary source. It's already structured for this purpose and its format is designed to match this skill's output one-for-one. Carry the R table and Decisions table over verbatim, preserving IDs and row order.

Otherwise, pull raw material from the broader conversation and map to these sections:

- **Problem**: any framing of what's broken, missing, or worth doing, and why now
- **User journey**: steps, flows, or user actions the conversation has touched on
- **Context**: domain or system background the reader needs before the requirements make sense (e.g. institution tiers, user cohorts, existing system quirks). Skip if not needed.
- **Requirements**: specific things the team has aligned on (look for "we need X", "it should do Y", or confirmation language from stakeholders)
- **Definition of done**: observable outcomes that mean this project shipped
- **Out of scope**: anything the team has explicitly said is not being done
- **Decisions**: resolved choices made during shaping. Look everywhere — not just where they're tidily labelled. Decisions often end up buried in open-question threads with a "Decision:" subheading inside discussion notes. Pull those out into the Decisions table; don't leave them in Open questions.
- **Design reference**: any mention of Figma, mocks, or prototypes
- **Open questions**: anything flagged as unresolved, TBD, or "need to check with someone". Strip out anything that's actually been decided — that goes in Decisions.

If a section has nothing useful in context, mark it as a gap.

### 3. Show a draft before creating

Share a draft preview with the user containing:

- Proposed project title (the feature or initiative name, not a sentence). For example, "Recommendations v2 experimentation", not "Create a PRD for recs v2".
- What you pulled from context for each section
- What's missing, and whether they want to fill it now or leave a placeholder

Wait for confirmation before creating. PRDs in Linear become a source of truth for the team, so it's cheaper to confirm the framing now than to fix a misaligned PRD later.

If the project name isn't obvious from context, propose 2 or 3 options rather than guessing.

### 4. Find the team

The Linear team is always **Product Eng & ML**. List the Linear teams, match the name (case-insensitive), and use that team ID.

If the team can't be found, stop and ask the user which team to use. Don't silently pick a fallback.

### 5. Find the Shaping project status

The project must always be created in the **Shaping** state. Linear projects have a status field that points to a named project status (Backlog, Planned, In Progress, etc., plus any custom statuses the workspace has added). "Shaping" is a custom status used by this team.

Look up the available project statuses for the Product Eng & ML team and find the one named "Shaping" (case-insensitive). Use that status ID when creating the project.

If no "Shaping" status exists, stop and ask the user how to proceed. Don't fall back to Backlog or Planned silently, because the team relies on the Shaping state to filter work in progress.

### 6. Create the project

Create the Linear project with:

- Name: the confirmed title
- Team: the Product Eng & ML team ID
- Status: the Shaping status ID
- Description / body: the markdown template below, filled in

### 7. Round-trip verification

After creation, fetch the project back with `get_project` and verify the description round-tripped intact. Linear's `save_project` has been seen to silently truncate long markdown tables.

Checks:

- The Requirements table still has the same number of rows you wrote, and the last row is intact
- The Decisions table still has the same number of rows you wrote, and the last row is intact
- All section headings are present

If anything is truncated or missing, re-save and verify again. Do not tell the user the PRD is ready until this check passes.

### 8. Confirm title and summary

Linear projects have a name and a short summary separate from the description. When shaping reshapes a project mid-flight, these often go stale while the description gets rewritten.

Show the user the current project name and summary (or the ones you're about to create) and ask whether they still fit the current scope. If not, update both alongside the description.

For new projects: propose a name and summary at the draft stage (step 3) and confirm as part of that draft.

For updates: compare the existing name and summary against the reshaped description before saving, and flag any drift explicitly.

### 9. Share the URL

Share the project URL back to the user. Keep the confirmation short. Don't re-summarise the PRD contents, because the user can click through.

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

## Context

[Optional. Domain or system background the reader needs before the requirements make sense — e.g. institution tiers, user cohorts, existing system quirks. Skip this section entirely if not needed; don't leave a placeholder.]

## Requirements

| ID | Requirement | Status | Notes |
| --- | --- | --- | --- |
| R0 | [aligned requirement] | Core goal / Must-have / Should-have / Could-have | [context] |

[If no requirements are aligned yet: _TBD: to be filled in after shaping._]

## Definition of done

- [Observable outcome that means this project shipped]

[If none captured yet: _TBD_.]

## Out of scope

- [Thing we're explicitly not doing]

[If none: _None captured yet._]

## Decisions

| Decided | Decision | Reason |
| --- | --- | --- |
| YYYY-MM-DD | [What was decided] | [One-line why] |

[If none: _None captured yet._]

## Design reference

<linear-embed url="[Figma URL]" />

_Paste Figma link here when ready. Linear will auto-embed it._

## Open questions

- [Question 1]
- [Question 2]
~~~

### Notes on each section

**Problem.** One short paragraph. This is the "why" that anchors the rest. Resist padding it out.

**User journey.** Dot points with sub-points for branching. If the flow is genuinely simple (2 or 3 steps), that's fine. Don't invent complexity.

**Context.** Optional. Include only when the reader genuinely needs a domain primer to understand the requirements. Examples: institution tiers, pricing bands, user cohorts, existing system quirks. When the project doesn't need one, omit the heading entirely — don't leave an empty placeholder.

**Requirements.** Only things the team has actually aligned on. Aspirations or ideas still being debated belong in Open questions. The table uses an ID column (R0, R1, ...) so requirements can be referenced consistently. The Status column values are Core goal, Must-have, Should-have, Could-have — nothing else. Non-goals and open questions are never statuses; they live in their own sections. If shaping has already assigned R IDs and statuses, preserve them verbatim.

**Definition of done.** Observable outcomes that mean this project shipped. Not tasks, not process steps. Prefer outcomes the team could verify from production: "members see only jobs they're assigned to", not "ship query filtering".

**Out of scope.** Explicit things not being done. The strongest defence against scope drift later. Carry across from shaping when present. If the team hasn't articulated any yet, leave the placeholder rather than inventing them.

**Decisions.** Resolved choices from shaping, with a one-line reason. Use ISO dates (e.g. `2026-04-21`), not relative phrasing like "last week". Keep each reason to one line. If a decision needs deeper reasoning, write a numbered ADR in the project's local `decisions/` folder (per the pm-workspace CLAUDE.md) and keep the table row as a short pointer. Pull resolved decisions out of Open questions — don't leave them double-tracked.

**Design reference.** Use a `<linear-embed url="..." />` block with the Figma URL when one exists. Otherwise leave the placeholder — the user pastes the URL later and Linear handles the embed natively.

**Open questions.** Genuine unresolved items for the team to work through together. Not rhetorical prompts, and not things you could answer yourself. Anything with a "Decision:" subheading buried inside its discussion has already been decided — move it to the Decisions table instead of leaving it here.

## Writing style

The user writes in Australian English and dislikes obvious AI writing tells. Apply this to all generated PRD content (problem statements, user journey steps, requirements, questions).

Specifics:

- Use Australian spellings: organise, prioritise, behaviour, centre, recognise, etc.
- Don't use em dashes (—) or en dashes (–). Use a full stop, comma, colon, parentheses, or just restructure the sentence.
- Don't use ellipses (...) for stylistic effect.
- Avoid the "it's not just X, it's Y" construction.
- Avoid throat-clearing phrases like "let's dive in", "in the world of", "navigate the landscape", "delve into".
- Write plainly. Short sentences are fine. Contractions are fine. The goal is content that reads like the user wrote it themselves at their desk.

## Updating an existing PRD

Triggered when Step 1 finds a matching project and the user confirms update rather than create. The flow:

1. Fetch the existing project and read its current body, name, and summary.
2. Draft the delta (what's changing in each section) and show it to the user before applying. Call out any decisions currently buried in Open questions and propose promoting them to the Decisions table.
3. When applying, preserve anything not explicitly being changed. Don't overwrite sections the conversation hasn't touched.
4. Don't change the project's team or status. If the project is no longer in Shaping, don't move it back.
5. Check the project name and summary against the updated scope. If they've gone stale (common after a reshape), prompt for updates to both alongside the description.
6. Run the round-trip verification from step 7 of the create flow. Re-save if anything was truncated.
7. Share the updated project URL back to the user.

## What to avoid

- Don't create a duplicate project when one already exists for this work. Update it instead.
- Don't create the project before the user has seen a draft and confirmed.
- Don't create the project in any state other than Shaping.
- Don't pad thin sections with filler to make them look substantial. A short honest section beats a bloated one.
- Don't invent requirements the team hasn't actually aligned on. If you're unsure whether something is aligned, put it in Open questions.
- Don't leave resolved decisions sitting in Open questions. Promote them to the Decisions table.
- Don't skip round-trip verification, even for small updates. Truncation has been seen on tables of a dozen rows or more.
- Don't rewrite the whole PRD in the chat after creating it. The URL is enough.
