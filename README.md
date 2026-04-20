# hatch-product-skills

Codified PM workflows at Hatch. A shared Claude plugin so every PM on the team produces consistent output when shaping problems, writing PRDs, and filing tickets.

## Why this exists

We want PRDs that look the same across PMs, tickets engineers can pick up without back-and-forth, and a shared language for shaping problems. These skills encode how we do that work, so Claude produces output that matches the team's standards regardless of who's driving.

## Install

In Claude Desktop / Cowork:

1. Add this repo as a marketplace:

   ```
   /plugin marketplace add hatch-team/hatch-product-skills
   ```

2. Install the plugin:

   ```
   /plugin install hatch-product-skills@hatch-product-skills
   ```

Updates pushed to `main` become available the next time the marketplace is refreshed.

## Skills

```
skills/
  shaping/           # thinking partner for shaping a problem
  prd/               # turn shaped thinking into a Linear project (PRD)
  ticket/            # draft implementation-ready Linear tickets
  triage-review/     # weekly sweep of Linear's Triage column
  feedback-review/   # weekly themed digest of Slack customer feedback
```

Intended flow: `shaping` → `prd` for new features, or straight to `ticket` for discrete work. `triage-review` and `feedback-review` run weekly (usually as scheduled tasks) and feed back into the rest of the loop.

## Contributing a skill

The goal is shared standards, so changes should reflect how the team actually wants to work, not personal preferences.

1. Create `skills/<skill-name>/SKILL.md`.
2. Frontmatter must have `name` and `description`. The description determines when the skill auto-triggers, so be specific about the trigger phrases.
3. Body is the instructions Claude follows when the skill runs.
4. Open a PR so the team can review before it becomes part of the shared workflow.

Branch-per-experiment is fine if you want to trial a skill before proposing it to the team.
