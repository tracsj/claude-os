# Command Examples

## Good command wrapper (`commands/newsletter.md`)

```markdown
---
description: Start a newsletter session
---

Before starting, read `.claude/skills/newsletter/learnings.md` and apply any guidance from the Staging section.

Run the newsletter skill at @.claude/skills/newsletter/SKILL.md.
```

## Rules

- **Commands are always wrappers — 5–10 lines max.** Their job: read the skill's learnings, invoke the skill, pass arguments. No logic. No decision trees.
- If a command file is growing beyond ~10 lines of logic, that logic belongs in a skill.
- Commands never have their own learnings files. All learnings live in `.claude/skills/<name>/learnings.md`. There is no `.claude/learnings/` directory.
- Always create a `/project:wrap-up` command regardless of what other commands the project has.
