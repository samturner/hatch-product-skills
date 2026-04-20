---
name: feedback-review
description: Review recent customer feedback from Slack and produce a themed digest with supporting quotes, volume signal, and what's changing vs the previous review. Use when the user asks for a "feedback review", "feedback digest", "feedback summary", wants to see "what customers have been saying" over a period, or when running as a scheduled task against the feedback channel. Default channel is #feedback, default window is the last 7 days — both can be overridden.
---

# Feedback review

Produces a structured digest of customer feedback from Slack so the team has a shared read on what's changing week to week. Designed to run weekly as a scheduled task, but also invokable on demand.

## Defaults

- **Channel:** `#feedback` — override if the user specifies a different one.
- **Window:** last 7 days — override if the user asks for a different period.
- **Canvas:** `Feedback digest` in the same channel, used as the running history and the comparison source.

## Workflow

### 1. Read the channel

Pull messages and thread replies from the channel across the window. Include threaded replies — they often contain the most substantive feedback. Ignore bot messages and the skill's own prior digest messages.

If the channel is quiet (fewer than ~5 pieces of distinct feedback), skip the full workflow and just post a one-line "Quiet week in #feedback, nothing material to report." Don't invent themes to fill space.

### 2. Cluster into themes

Group feedback into themes. A good theme:

- Has a concrete, specific name (e.g. "Billing page confusion on downgrade", not "UX issues")
- Is backed by at least 2 distinct pieces of feedback. Single one-offs go in a final "Notable one-offs" section rather than inflated into a theme
- Captures what the customer is actually experiencing, not a proposed solution

For each theme, capture:

- Theme name
- Rough volume (number of distinct pieces of feedback this window)
- 2-3 representative quotes with author and a Slack permalink to the **thread** (not the individual message), so the reader can click through and see the full discussion, replies, and any context from the team. If the feedback is a standalone message with no thread, link to the message itself.
- 1-line summary of the underlying issue

### 3. Load the prior digest

Find the most recent section in the `Feedback digest` canvas in the channel. Use it as the baseline for the comparison step.

If no prior canvas exists (first run), skip the comparison — just note "First digest, no prior baseline." Don't invent trends.

### 4. Identify what's changing

Compare this window's themes to the prior digest:

- **New themes:** present this week, not in the previous digest
- **Trending up:** volume meaningfully higher than last time
- **Gone quiet:** present last time, absent or near-zero now
- **Steady:** present at similar volume — worth listing briefly, but don't dwell

Be honest about signal. Small shifts within noise aren't trends. If you can't tell, say so rather than forcing a narrative.

### 5. Compose the digest

Format as markdown:

```
# Feedback digest — [date range]

## TL;DR
[1-2 sentence summary of what changed and what matters most this week]

## What's changing
- **New:** [theme name] — [1 line]
- **Trending up:** [theme name] — [1 line, with volume delta]
- **Gone quiet:** [theme name] — [1 line]

## Themes this week

### [Theme name] — [volume] mentions
[1-line summary]

> [Quote 1] — [@author](thread-permalink)
> [Quote 2] — [@author](thread-permalink)

### [Next theme...]

## Notable one-offs
- [Short description] — [@author](thread-permalink)

All permalinks should point to the Slack **thread** where the feedback lives, so the reader can click through and see the full conversation in context.
```

Keep it scannable. The team will read this quickly on a Monday morning — favour concrete over comprehensive.

### 6. Post the digest

**If invoked interactively (on-demand run):** show the digest to the user first, let them edit or approve, then post.

**If run by the scheduler:** post automatically without review.

To post:

1. Update the `Feedback digest` canvas in the channel — prepend this week's section above the previous one so history reads newest-first.
2. Post a short message in the channel linking to the canvas with the TL;DR. Example: `Weekly feedback digest is up — TL;DR: [one sentence]. Full canvas: [link]`.

## Writing style

- Australian English (organise, behaviour, prioritise)
- Plain language. Short sentences are fine
- No em dashes (—) or en dashes (–). Use full stops, commas, or parentheses
- No "it's not just X, it's Y"
- No throat-clearing ("let's dive in", "in the world of", "navigate the landscape")
- Quotes should feel like customer voice, not paraphrased. Lift them verbatim when possible

## What to avoid

- Don't invent themes to fill space in a quiet week
- Don't summarise quotes into blandness. The raw customer phrasing is the value
- Don't suggest solutions. This skill reports the signal, the team decides what to do with it
- Don't skip the comparison step when a prior digest exists — "what's changing" is the most valuable section
- Don't post without the canvas update. A TL;DR message with no linkable detail isn't useful to anyone who wants to dig in
