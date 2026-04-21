---
name: cycle-planning
description: Fill out the OKR2 section, Other cycle priorities, and LT discussion points in the fortnightly cycle planning doc in Notion. Pulls project status from the Hatch Students Linear board, drafts updates, asks clarifying questions for anything Linear can't confirm, and waits for approval before writing to Notion. Use when the user asks to "fill out the cycle doc", "update cycle planning", "do OKR2 for the cycle", or names a specific cycle (e.g. "fill out Cycle 9").
---

# Cycle planning

Fortnightly PM chore: update the next cycle's planning doc in Notion so the LT has a clear picture of OKR2 status, what's shipping, what's starting, and what's up for discussion. Much of it can be drafted from Linear, but parts only live in the PM's head, so always draft first and ask before writing.

## Scope

This skill covers three parts of the cycle doc:

1. **OKR2: Test university program experience** — key results status, last cycle's check-offs, next cycle's plan (expect to ship / start build on / start shaping).
2. **Other cycle priorities** — catch-all column the PM maintains.
3. **Other LT discussion points** — anything to surface at the LT cycle review.

OKR1 / OKR3 / OKR4 are owned by other people and pull from different sources. Don't touch them.

## Inputs

- **Cycle doc URL or number.** Ask the user which cycle if not given. Cycle docs live under the "Cycle planning" parent in Notion: `https://www.notion.so/hatchteam/Cycle-planning-2beefc5f72be801a9f73f2f9329423d9`. Each cycle is a child page titled "Cycle N".
- **Linear board:** Product Eng & ML team, projects with the `Hatch Students` label.

## Workflow

### 1. Fetch the cycle doc and Linear projects

In parallel:

- Fetch the Notion cycle doc.
- List Linear projects for team `Product Eng & ML`, filter to projects with the `Hatch Students` label. If the Linear tool returns "Query too complex", drop `includeMilestones` / `includeMembers`. If the result is too large, save to disk and filter with `jq`.

From the doc, note:

- Current status of each OKR2 key result (some may say `todo: fill out`, some may say `n/a`, some may already have status).
- Current cycle number (e.g. Cycle 9) and previous cycle number (Cycle 8).
- Cycle N-1 bullets (for check-offs) and the empty Cycle N column.
- What's already in Other cycle priorities and LT discussion points.

### 2. Classify Linear projects by status

Map Linear project `status.name` to the cycle plan buckets:

- **Build** → candidate for "Expect to ship this cycle".
- **Shaping** → candidate for "Start build on this cycle".
- **Roadmap** → candidate for "Start shaping / design on this cycle".
- **Released / Canceled / Paused / Backlog / Ongoing** → ignore for the draft.

This is a starting classification. The user may move things between buckets (e.g. a Shaping item still needs more shaping and won't go to Build yet). Never force a project into a bucket without confirming.

### 3. Draft OKR2 updates

**Key results:**

- For each KR marked `todo: fill out`, propose a status using the legend: ✅ done, 🟢 on track, 🟡 at risk, 🔴 off track.
- Base the status on project progress across the loop the KR describes. For the end-to-end community KR, check each stage (verified sign-up → discovery / pre-vetting → employer engagement → hires / placement) has at least one project in flight or shipped.
- **Leave `n/a` KRs alone.** They're intentionally out of scope for this cycle.
- If you can't confidently judge a KR's status from Linear, say so and ask the user.

**Previous cycle (Cycle N-1) column:**

- For each bullet, propose a status marker (✅ / 🟡 / 🔴) and a short note.
- **Always ask** about anything Linear can't confirm: sessions held, feedback meetings, external conversations, whether something "kicked off" beyond a status change. Don't guess.
- For bullets that map to Linear projects, note the status change (e.g. a project reaching Build is evidence of design/reqs being ready).

**Next cycle (Cycle N) column:**

Structure as three groupings, each project linked to its Linear URL:

```
**Expect to ship**
- [Project name](linear URL)

**Start build on**
- [Project name](linear URL)

**Start shaping / design on**
- [Project name](linear URL)
```

For each grouping:

- Pull the classified Linear projects from step 2.
- Verify with the user: is "ship by end of cycle" realistic for each Build project, or should some move to "continue build"? Which Shaping projects are genuinely starting Build vs. continuing to shape?
- If the "start shaping" list is long, offer to leave it open ("1–2 of: ...") rather than committing to a full list. The user often has a team conversation about what's next and doesn't want to pre-empt it.

### 4. Prompt for Other cycle priorities

Show what's currently in the Cycle N column (usually near-empty). Ask:

- Anything to add? (common examples: hiring initiatives, cross-team asks, ops work, one-off events)
- Anything to check off or note on the Cycle N-1 column?

Don't invent items. Write only what the user provides.

### 5. Prompt for Other LT discussion points

Ask what the user wants to surface at the LT cycle review. Examples: decisions needed, risks to flag, strategic questions, updates from other teams.

Don't invent items. Write only what the user provides.

### 6. Present the full draft

Show everything in one message: KR statuses, Cycle N-1 check-offs, Cycle N sections, Other priorities additions, LT discussion points. Call out any open questions the user still needs to answer.

Wait for explicit approval. Accept edits, omissions, or rewording before applying.

### 7. Apply to Notion

Use `notion-update-page` with `update_content` command. One `content_updates` batch with multiple old_str / new_str pairs is fine.

Keep the existing page structure intact:

- Don't touch table widths, column definitions, or other OKRs.
- Preserve tabs and indentation inside `<column>` blocks.
- Match markdown link format `[title](url)` for Linear projects.
- Use the status emoji legend from the top of the doc (✅ 🟢 🟡 🔴), not custom symbols.

After applying, re-fetch the page and confirm the changes landed as expected. If something didn't match, fix and retry rather than declaring success.

## Writing style

- Australian English (organise, prioritise, behaviour).
- Short sentences. Plain language.
- No em dashes (—) or en dashes (–). Use commas, parentheses, full stops.
- Match the voice already in the doc: terse, emoji-led status, no throat-clearing.

## What to avoid

- Don't fill in `n/a` key results. They're intentional.
- Don't guess whether sessions, meetings, or external conversations happened. Ask.
- Don't commit a long "start shaping" list without checking — the user usually wants this left open.
- Don't write to Notion without explicit approval. Draft first, confirm, then apply.
- Don't touch OKR1 / OKR3 / OKR4 or their columns. Out of scope.
- Don't invent entries for Other priorities or LT discussion points. If the user has nothing to add, leave those sections as-is.
- Don't rewrite the page structure (tables, columns, headers). Only change content inside the targeted cells and columns.
- Don't claim success if the re-fetch shows the update didn't land. Retry with corrected old_str.
