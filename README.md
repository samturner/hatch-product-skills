# hatch-product-skills

Sam's personal Claude plugin — a bundle of skills for product work at Hatch.

## Install

In Claude Desktop / Cowork:

1. Add this repo as a marketplace:

   ```
   /plugin marketplace add <github-user>/hatch-product-skills
   ```

2. Install the plugin:

   ```
   /plugin install hatch-product-skills@hatch-product-skills
   ```

Updates pushed to `main` become available the next time the marketplace is refreshed.

## Structure

```
.claude-plugin/
  plugin.json          # plugin manifest
  marketplace.json     # makes the repo installable as a marketplace
skills/
  weekly-review/
    SKILL.md           # one skill per folder
```

## Adding a new skill

1. Create `skills/<skill-name>/SKILL.md`.
2. Frontmatter must have `name` and `description`. The description determines when the skill auto-triggers — be specific about the trigger phrases.
3. Body is the instructions Claude follows when the skill runs.
4. Commit and push.

## Sharing with the team

Anyone at Hatch can add the same marketplace URL and install the plugin. Branch-per-experiment if you want to trial skills before merging to `main`.
