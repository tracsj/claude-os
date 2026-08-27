---
name: build-skill
description: Create a new Claude Code skill for any project. Use when asked to add, create, or build a skill. Do NOT use for commands (simple .md wrappers — no logic needed) or for agents (use build-agent instead).
---

Before starting, read learnings.md and apply any guidance from the Staging section.

# Build Skill

Creates a complete, well-structured skill for any project.

## Discovery

Ask for each skill:
- What recurring work does this skill handle?
- What does great output look like?
- What is the step-by-step process?
- Which context files does it need, and at which step?
- What mistakes should it avoid?
- Are there examples of good past output to reference?

## Rules

- SKILL.md targets under 200 lines — deep knowledge goes in spoke files (`examples/`, `checklist.md`, reference docs). Treat it as a budget rather than a hard limit: a hub that runs long is a signal to check what could move to a spoke, not a reason to cut something load-bearing
- Description = trigger — write for the model, not humans; always include a negative trigger ("Do NOT use for...")
- **Every SKILL.md must start with:** `Before starting, read learnings.md and apply any guidance from the Staging section.`
- **Every SKILL.md must end with a Final step — Capture learnings section**
- **Every skill needs a verification step** — if it produces output, include a way to check quality before presenting it
- **Skills that read spoke files (context/, design-system.md, etc.) must also ask whether those files need updating at close.** Consumption without maintenance creates silent drift. Add a mandatory check: "Did anything this session make [spoke file] stale? If yes, update it now."
- **Decide global vs project scope before writing a line.** Ask: does this apply to every project, or to one? If the skill references specific file paths, CSS tokens, a palette, a schema, or a single codebase, it is project-scoped and belongs in that repo's `.claude/skills/` — not `~/.claude/skills/`. The pull toward global is strong because the skill *feels* reusable while you are writing it; the content is the evidence, not the feeling. The failure that produced this rule: a UX skill written at global scope whose every substantive line named one project's tokens, paths and palette. It read as reusable and was not.
- **Folder-scoped skills register in the folder's CLAUDE.md, not root.** If the skill operates primarily within one subfolder of a project (e.g. `website-ux` for `website/`, `create-blog-post` for blog work), reference it in *that folder's* CLAUDE.md or via the folder's own `.claude/`, not at the project root. Root-level skill mentions are reserved for skills that apply across every folder of the project — in practice that is usually just `wrap-up`. Genuinely cross-folder skills are rare, so if you are about to register one at root, check first that it isn't really scoped to a single folder's workflow.

## File structure to create

```
.claude/skills/<name>/
  SKILL.md          ← hub: target under 200 lines
  learnings.md      ← stub only: # <name> — learnings\n\n## Staging
  [examples/]       ← only if examples exist
  [checklist.md]    ← only if a checklist is needed
```

## Reference

Read `../claude-setup/examples/skill.md` — a sibling skill in this plugin — for a reference SKILL.md and
learnings.md structure before writing.

## Verification

Before presenting the finished skill, check:
- [ ] Description has a negative trigger
- [ ] First line reads learnings.md
- [ ] Last section is Capture learnings
- [ ] Verification step is present
- [ ] Targets under 200 lines; if longer, the overflow is genuinely hub material
- [ ] If skill reads a spoke file: Final step asks whether that file needs updating

## Final step — Capture learnings

If anything worked particularly well or went wrong, append a dated entry under `## Staging` in `learnings.md`. Apply promotion + pruning per `CONVENTIONS.md` at the plugin root.
