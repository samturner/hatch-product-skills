---
name: weekly-review
description: Draft Sam's weekly product review. Use when the user asks for a "weekly review", "Friday wrap-up", or wants to summarise the week's product progress. Pulls together what shipped, what's in flight, blockers, and next week's focus in Sam's preferred format.
---

# Weekly review

This is a stub skill — replace the body below with the actual instructions Claude should follow.

## When this runs

Triggered when Sam asks for his weekly product review.

## Steps

1. Ask Sam which week to cover if unclear (default: current week Mon–Fri).
2. Pull context from relevant sources (Linear, Slack, etc.) — TODO: specify which.
3. Draft the review in the following format:

   - **Shipped this week** — bullets of what went out
   - **In flight** — what's progressing, with status
   - **Blockers** — anything stuck + what would unblock it
   - **Next week** — top 3 priorities

4. Keep it concise. Australian English. Metric units if any measurements come up.

## Output

Post as a Slack canvas or return as markdown — ask Sam which.
