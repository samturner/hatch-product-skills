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

Three modes. Pick based on how the skill was invoked. **Approval before any Linear write is required in both interactive and scheduled modes.** The skill never silently applies changes.

- **Interactive:** user asked for a triage review directly in chat. Produce the breakdown, get approval, then apply.
- **Scheduled:** run by the scheduler. Produce the breakdown and wait for approval before applying. Surface the breakdown however the scheduler delivers output (chat, email, etc.) and hold until the PM approves. Do not auto-apply just because there's no live prompt.
- **Test mode:** user asked for a test, dry run, preview, or "show me what you'd change". Produce the full proposed-changes report and **do not touch Linear** — no label changes, no priority changes, no status changes, no project assignment, no duplicate links. Prefix the output with a clear header so the user knows nothing was applied (see Step 5).

Read the user's invocation to decide. "Review triage" → interactive. "Test the triage review" or "dry run triage" → test mode. Anything run without a user prompt → scheduled.

## Workflow

### 1. Pull the triage queue

List all tickets currently in Triage status for the target team. Keep the list — you'll report against it at the end.

If Triage is empty, return a one-liner ("Triage is clear, nothing to review") and stop.

### 2. For each ticket, assess three things

**a. Is it a probable duplicate?**

Search Linear for open tickets with similar titles or descriptions. Be conservative. A "probable duplicate" is where a reasonable reader would say "this is already tracked" — not merely related or thematically close.

If yes, note the candidate duplicate's ID and title. Don't silently merge.

**b. Is it pickup-ready?**

Pickup-ready means the ticket is clear enough that an engineer or coding agent could start work without back-and-forth. This is the bar for moving out of Triage.

A ticket is pickup-ready when:

- For a **bug**: the problem is clear, who's affected is clear, and there's either a repro path or specific enough detail to investigate
- For a **feature/improvement**: the problem it solves is articulated, who benefits is clear, and there's at least a solution direction

A ticket is not pickup-ready when any of these are missing:

- No clear problem statement (just "X is broken" with no detail)
- No signal on impact (one user? many? blocking a flow? a minor annoyance?)
- Bug with no repro and no environment context
- Feature request with no underlying problem, only a proposed solution

List the specific gap(s). This goes into the end-of-run report so the PM can follow up.

**c. What's the right priority?**

Use the team's shared rubric. Read `plugins/hatch-product-skills/shared/priority.md` for the levels and the pressure-test questions. When in doubt, bias toward **Medium**.

**Always reassess priority from scratch.** If the ticket already has one set (Triage tickets often do, from whoever created them), treat it as a starting hypothesis, not a fixed input. If your assessment differs, change it. If you aren't confident on the right level but are confident the existing priority is wrong, clear it rather than leaving a misleading value. Flag the correction in the report so the PM can sanity-check.

### 3. Build the set of proposed changes

For each ticket, the outcome is one of:

- **Prioritise and move out of Triage.** Requires both: (a) confident on the priority, and (b) the ticket is pickup-ready. No duplicate concern. Set (or correct) the priority, move status from Triage to the team's standard next-stage status (usually Backlog, look it up, don't guess), and add the ticket to the landing project (`Bugs, polish, tech debt and incidents` by default). If the ticket clearly belongs to a different existing project (for example it's part of a shaped PRD), add it there instead and note the choice in the report.
- **Label `needs-info`, leave in Triage.** Not pickup-ready, or not enough info to be confident on priority. Apply label. Don't comment on the ticket (team preference is label only).
- **Label `possible-duplicate`, link the candidate, leave in Triage.** Apply label, add a relates-to link to the candidate duplicate, leave for the PM to confirm and merge.

A ticket can receive both labels (e.g. possible duplicate *and* missing info).

**Cross-cutting: correct wrong priorities regardless of outcome.** If a Triage ticket has a clearly wrong priority, fix it even when the ticket isn't moving out. Change it to the right level if you're confident, or clear it if you aren't. Don't leave a misleading priority on a ticket that isn't ready to move.

### 4. Present the breakdown and wait for approval

**Test mode:** skip this step entirely. Go straight to Step 5 and produce the report with the test-mode header. Do not touch Linear.

**Interactive and Scheduled:** always produce the breakdown first and wait for explicit approval before any Linear write. The breakdown is a compact table covering every ticket in the queue:

| Ticket | Title | Current priority | Proposed action | Rationale |
| --- | --- | --- | --- | --- |

The `Proposed action` column should name the outcome (e.g. "Prioritise High, move to Backlog, add to Bugs project"; "Clear priority, label `needs-info`"; "Label `possible-duplicate`, link HATCH-234"; "No change"). The `Rationale` column should be specific ("blocks checkout for all users on Safari"), not generic ("important bug").

The PM can approve the batch, skip specific tickets, or override any call. Do not silently auto-apply, even in scheduled mode — the scheduler surfaces the breakdown through whatever channel it uses (chat, email) and the skill holds until the PM responds.

Once approved, apply:

1. Look up the relevant label IDs (`needs-info`, `possible-duplicate`) for the team. If a label doesn't exist, ask the PM before creating it.
2. Look up the status ID for the next stage (usually Backlog).
3. Look up the landing project ID (`Bugs, polish, tech debt and incidents`) by name. If it can't be found, stop and ask the PM before proceeding.
4. Apply label changes, priority changes (including corrections to existing priorities and clears), status changes, project assignment, and duplicate relations.

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

## Priority corrections (tickets still in Triage)
- [Ticket ID]: [title] — was [old priority], now [new priority or "cleared"] because [1-line reason]

[Omit this section if no corrections were made.]

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

- Don't apply any Linear write without an explicit approval, in any mode (except test mode, which doesn't write at all). Scheduled runs hold for approval just like interactive ones.
- Don't defer to the priority that was set at ticket creation. Reassess every time. If it's wrong, correct it or clear it.
- Don't move a ticket out of Triage unless both (a) you're confident on the priority and (b) the ticket is pickup-ready. Missing either → leave in Triage.
- Don't silently merge duplicates. Label and link so the PM decides.
- Don't comment on tickets. Team preference is label only.
- Don't prioritise a ticket you can't confidently assess. When in doubt, label `needs-info` and list the gap.
- Don't invent urgency to make the queue look busier.
- Don't skip the duplicate check. Dupes in the backlog cost more than they look like they should.
- Don't report on tickets you didn't actually touch. The report should match what happened in Linear.
