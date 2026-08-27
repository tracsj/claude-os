---
name: claude-setup-subfolder
description: Set up a well-structured work area inside an existing project that already has a .claude/ folder. Use when asked to set up a subfolder, add a new work area, or bootstrap a subfolder CLAUDE.md. Do NOT use for brand-new projects with no .claude/ — use claude-setup instead. Do NOT duplicate root-level settings, memory, or skills — only add what is specific to this subfolder.
---

Before starting, read [claude-setup-subfolder learnings](learnings.md) and apply any guidance from the Staging section.

# Subfolder Setup

Set up a well-structured work area inside an existing project that already
has a `.claude/` folder. Work interactively, waiting for input before saving
anything. The root project already handles settings, memory, context, and
commands — don't duplicate any of it.

---

## PHASE 1 — UNDERSTAND THE AREA

Ask:
- What is this subfolder for? What work happens here?
- Does it need a different tone or set of never-rules from the root project?
- Will there be recurring structured work that needs a skill?
- Would a tracker or progress file be useful here? If yes, we'll create it as part of setup and reference it from CLAUDE.md.

**If this area is an app or a feature that adds data** (entities, relationships, a source of truth), consider running `/shape-app` first — it shapes the model + invariants before code.

---

## PHASE 2 — DRAFT THE CLAUDE.md

Write a subfolder `CLAUDE.md`. Rules:
- First line (after the heading): a blockquote pointing to the parent: `> For overall project context, see ../CLAUDE.md and ../context/.` (adjust depth — `../../` if nested two levels down)
- Context only — what this area is, what tone applies, what never to do here
- Under 20 lines
- Reference any tracker/progress files with `@path/to/file`
- No process instructions — those belong in skills
- Every line must pass: "Would removing this cause Claude to make a mistake?" If not, cut it

Show for approval before saving.

---

## PHASE 3 — SKILLS (if needed)

If there's recurring structured work, first check whether an existing skill in root `.claude/skills/` already covers it. If yes, note that in the summary and skip to Phase 4. If no, invoke the `build-skill` skill — it handles the discovery questions, authoring rules, and file structure.

Note: skills always live in root `.claude/skills/<name>/` — never inside the subfolder.

---

## PHASE 4 — COMMAND SHORTCUT (optional)

If a slash command shortcut would be useful, propose one in root `.claude/commands/<name>.md`.
Ask before creating.

**A command that only redirects to a skill adds nothing** — skills are invoked directly from their description. Commands earn their place when they add something: passing `$ARGUMENTS`, combining multiple steps, or providing context the skill doesn't have on its own. If the only content would be "run this skill," skip Phase 4.

---

## PHASE 5 — REVIEW & CREATE

Show the full proposed file list before writing anything:

```
[subfolder]/
  CLAUDE.md
.claude/skills/[name]/   ← if a skill was needed
  SKILL.md
  learnings.md
.claude/commands/        ← if a shortcut was needed
  [name].md
```

Get approval, then create everything in one pass.

## Verification

After creating, confirm before presenting as done:
- [ ] The subfolder `CLAUDE.md` states only what is true of *this* area — nothing duplicated from root
- [ ] Any new skill lives in root `.claude/skills/`, not inside the subfolder
- [ ] Any new skill has a negative trigger, opens by reading `learnings.md`, and has a verification step of its own
- [ ] The subfolder's `CLAUDE.md` names the skill if a session working here needs to know it exists

---

## PHASE 6 — IMPROVE THIS SKILL

After setup is complete, ask:
- Did any phase feel unclear or produce the wrong output?
- Was anything missing?

If yes, append a dated entry under `## Staging` in `learnings.md` and apply promotion + pruning:
- If the issue is likely to recur → edit `SKILL.md` immediately; delete the Staging entry
- If one-off → leave in Staging only
- If older than 30 days unpromoted → delete

## Final step — Capture learnings
If anything worked particularly well or went wrong, append a dated entry under `## Staging` in `learnings.md`. Apply promotion + pruning per `CONVENTIONS.md` §3.

> **Global principles**: if this setup session surfaced something that should govern all future projects rather than only this one, it belongs at user scope in `~/.claude/CLAUDE.md`, which loads into every session in every project. Put it through the 3-question filter in `CONVENTIONS.md` §8 first — most things that feel universal while you are writing them are not.
