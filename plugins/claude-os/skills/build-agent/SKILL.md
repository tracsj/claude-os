---
name: build-agent
description: Create a new Claude Code agent definition for any project. Use when asked to add, create, or define an agent. Do NOT use for skills (use build-skill instead) or simple slash commands.
---

Before starting, read learnings.md and apply any guidance from the Staging section.

# Build Agent

Creates a well-defined agent for any project.

## Discovery

Ask:
- What self-contained task will this agent handle?
- Does it run unsupervised and report back, or does it need back-and-forth? (Back-and-forth → skill instead)
- Does it write or modify files/code? (Yes → requires `isolation: worktree`)
- Is it purely verification or research with no interaction? (Strong agent candidate)

## Decision guide

| Situation | Right choice |
|-----------|-------------|
| Single focused task, linear work | Skill or inline prompt |
| Self-contained, runs unsupervised, reports back | Agent |
| Two agents need each other's output directly | Agent team |
| Separable research + implementation parts | No definition — append "use subagents" to the prompt |

## Rules

- Description = trigger — same as skills; always include a negative trigger
- Any agent that writes or modifies code: add `isolation: worktree` in frontmatter
- Keep the prompt focused — agents can't ask follow-up questions mid-run

## File to create

```
.claude/agents/<name>.md
```

Frontmatter:
```yaml
---
name: agent-name
description: What it does + when to use + "Do NOT use for..."
[model: claude-sonnet-5]   ← optional; omit to inherit the session model
[isolation: worktree]      ← only for code-writing agents
---
```

## Reference

Read `../claude-setup/examples/agents.md` — a sibling skill in this plugin — before writing.

## Verification

Before presenting:
- [ ] Description has a negative trigger
- [ ] Code-writing agents have `isolation: worktree`
- [ ] Prompt is focused enough to run unsupervised
- [ ] Agent is the right choice (not a skill or inline "use subagents")

## Final step — Capture learnings

If anything worked particularly well or went wrong, append a dated entry under `## Staging` in `learnings.md`. Apply promotion + pruning per `CONVENTIONS.md` at the plugin root.
