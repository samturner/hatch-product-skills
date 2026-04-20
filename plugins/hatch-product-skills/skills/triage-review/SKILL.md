---
name: triage-review
description: Review tickets sitting in Linear's Triage status. For each ticket, either prioritise and move it out of Triage (if it has enough info) or flag it for clarification (if it doesn't). Also catches likely duplicates. Use when the user asks to "review triage", "triage the backlog", "clean up triage", or when running as a scheduled task. Also supports a test mode that produces the full proposed-changes report without touching Linear — trigger with phrases like "test", "dry run", "preview", or "show me what you'd change". Default team is Product Eng & ML — can be overridden.
---

# Triage review

Works through Linear's Triage column and does the scutwork so the PM can focus on judgement calls. Designed to run weekly as a scheduled task, but also invokable on demand.

## Defaults

- **Team:** Product Eng & ML (look up by name, case-insensitive, to get the team ID)
- **Scope:** every ticket currently in Triage status for that team
- **Info label:** `needs-info` — applied to tickets lacking enough detail to prioritise
- **Duplicate label:** `possible-duplicate` — applied to tickets that look like existing work
- **Landing project:** `Bugs, polish, tech debt and incidents` ([link](https://linear.app/hatchteam/project/bugs-polish-tech-debt-and-incidents-35fd3c010f52/overview)). The default home for tickets moved out of Triage. Look up by name to get the project ID.

## Run modes

Three modes. Pick based on how the skill was invoked.

- **Interactive:** user asked for a triage review directly in chat. Produce the proposed-changes table, get approval, then apply.
- **Scheduled:** run by the scheduler. Apply changes directly and produce the report.
- **Test mode:** user asked for a test, dry run, preview, or "show me what you'd change". Produce the full proposed-changes report and **do not touch Linear** — no label changes, no priority changes, no status changes, no duplicate links. Prefix the output with a clear header so the user knows nothing was applied (see Step 5).

Read the user's invocation to decide. "Review triage" → interactive. "Test the triage review" or "dry run triage" → test mode. Anything run without a user prompt → scheduled.

## Workflow

### 1. Pull the triage queue

List all tickets currently in Triage status for the target team. Keep the list — you'll report against it at the end.

If Triage is empty, return a one-liner ("Triage is clear, nothing to review") and stop.

### 2. For each ticket, assess three things

**a. Is it a probable duplicate?**

Search Linear for open tickets with similar titles or descriptions. Be conservative. A "probable duplicate" is where a reasonable reader would say "this is already tracked" — not merely related or thematically close.

If yes, note the candidate duplicate's ID and title. Don't silently merge.

**b. Does it have enough info to prioritise?**

A ticket has enough info when:

- For a **bug**: the problem is clear, who's affected is clear, and there's either a repro path or specific enough detail to investigate
- For a **feature/improvement**: the problem it solves is articulated, who benefits is clear, and there's at least a solution direction

A ticket lacks enough info when any of these are missing:

- No clear problem statement (just "X is broken" with no detail)
- No signal on impact (one user? many? blocking a flow? a minor annoyance?)
- Bug with no repro and no environment context
- Feature request with no underlying problem, only a proposed solution

List the specific gap(s). This goes into the end-of-run report so the PM can follow up.

**c. If it has enough info, what priority?**

Use the team's shared rubric. Read `plugins/hatch-product-skills/shared/priority.md` for the levels and the pressure-test questions. When in doubt, bias toward **Medium**.

### 3. Build the set of proposed changes

For each ticket, the outcome is one of:

- **Prioritise and move out of Triage.** Has enough info, no duplicate concern. Set priority, move status from Triage to the team's standard next-stage status (usually Backlog, look it up, don't guess), and add the ticket to the landing project (`Bugs, polish, tech debt and incidents` by default). If the ticket clearly belongs to a different existing project (for example it's part of a shaped PRD), add it there instead and note the choice in the report.
- **Label `needs-info`, leave in Triage.** Missing info to prioritise. Apply label. Don't comment on the ticket (team preference is label only).
- **Label `possible-duplicate`, link the candidate, leave in Triage.** Apply label, add a relates-to link to the candidate duplicate, leave for the PM to confirm and merge.

A ticket can receive both labels (e.g. possible duplicate *and* missing info).

### 4. Apply (behaviour depends on run mode)

**Test mode:** skip this step entirely. Go straight to Step 5 and produce the report with the test-mode header. Do not touch Linear.

**Interactive:** show the proposed changes to the PM as a compact table — ticket ID, title, proposed action, rationale. Get approval before applying. Allow the PM to skip specific tickets.

**Scheduled:** apply directly. The PM reviews the end-of-run report and can reverse anything they disagree with.

When applying (interactive after approval, or scheduled):

1. Look up the relevant label IDs (`needs-info`, `possible-duplicate`) for the team. If a label doesn't exist, ask the PM before creating it.
2. Look up the status ID for the next stage (usually Backlog).
3. Look up the landing project ID (`Bugs, polish, tech debt and incidents`) by name. If it can't be found, stop and ask the PM before proceeding.
4. Apply label changes, priority changes, status changes, project assignment, and duplicate relations.

### 5. Report

In **test mode**, prefix the report with:

```
> **TEST MODE — nothing has been changed in Linear.**
```

And change the section headings from past tense to conditional — "Would prioritise and move out", "Would flag `needs-info`", "Would flag `possible-duplicate`" — so it's unambiguous these are proposals.

Otherwise, output a compact markdown report:

```
# Triage review — [date]

**Reviewed:** [N] tickets in Triage.

## Prioritised and moved out
- **[Priority]** — [Ticket ID]: [title] — [1-line rationale]

## Flagged `needs-info`
- [Ticket ID]: [title] — missing: [specific gap(s)]

## Flagged `possible-duplicate`
- [Ticket ID]: [title] — likely duplicate of [Other ID]: [other title]

## Notes
[Anything worth surfacing. Omit this section if there's nothing to say.]
```

Keep it scannable. The PM reads this in Claude — no need for preamble or sign-off.

## Writing style

- Australian English (organise, behaviour, prioritise)
- Plain language. Short sentences are fine.
- No em dashes (—) or en dashes (–). Use full stops, commas, or parentheses.
- No throat-clearing ("let's dive in", "in the world of")
- Rationale lines should be specific ("blocks checkout for all users on Safari") rather than generic ("important bug")

## What to avoid

- Don't silently merge duplicates. Label and link so the PM decides.
- Don't comment on tickets. Team preference is label only.
- Don't prioritise a ticket you can't confidently assess. When in doubt, label `needs-info` and list the gap.
- Don't invent urgency to make the queue look busier.
- Don't skip the duplicate check. Dupes in the backlog cost more than they look like they should.
- Don't report on tickets you didn't actually touch. The report should match what happened in Linear.
