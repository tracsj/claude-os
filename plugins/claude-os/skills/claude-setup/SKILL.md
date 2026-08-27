---
name: claude-setup
description: Walk through setting up a well-structured .claude/ folder for a new project. Use when asked to set up Claude for a project, bootstrap a .claude/ folder, or run new project setup. Do NOT use if a .claude/ folder already exists — use claude-setup-subfolder instead.
---

Before starting, read [claude-setup learnings](learnings.md) and apply any guidance from the Staging section.

# New Project Setup

Walk me through setting up a well-structured `.claude/` folder for this
project. Work interactively, one phase at a time, waiting for my input
before proceeding. Be opinionated — if something doesn't need a skill,
command, or agent, say so. The goal is a lean system where the right
context loads at the right time and nothing more.

---

## BEFORE PHASE 1 — is the design already settled?

If an approved plan, brief or prior session already fixes the design — purpose, folder structure, skill scopes, permissions posture — do not walk phases 1–9 as interview questions. Read the plan, then go straight to **Phase 10** and build, using the phases as a completeness checklist rather than a script. Ask only where the plan is genuinely silent, and ask for those in **one batch** rather than phase by phase.

The parts a plan almost never settles, so expect these to be the real questions: anything that must be in the user's own words (a purpose file, a voice sample), and current state the plan could not know (what has been covered so far, what already exists on disk).

## PHASE 1 — PROJECT CONTEXT

Ask me:
- What is this project and what are its main goals?
- Who is the audience?
- What should Claude always/never do here?
- What tone, voice, or standards apply?
- What is the tech stack or primary content type?
- **If this is a learning, research or knowledge repo:** are raw source material and demonstrated understanding separate layers? A single notes pile is the default assumption and it is usually wrong — one layer records what a source said, the other records what the user has shown they understand, and only the second one is evidence. Getting this wrong bakes in a design where the model's own synthesis gets filed as the user's knowledge and then quizzed back at them.

**If this is an application**, consider running `/shape-app` first — it shapes the data model + invariants before any code, and its build-time invariants become the always/never rules this phase asks about.

Draft a root `CLAUDE.md` from my answers. Show for approval before saving. Rules:
- Target under 200 lines — if it runs longer, check what belongs in a reference file
- Context only — what is true about this project; no process instructions
- Every line must pass: "Would removing this cause Claude to make a mistake?" If not, cut it
- Use Mermaid diagrams for workflows or system structures
- **Do not add a `## Self-Learning Rule` block to a project CLAUDE.md.** State the learnings loop once, at user scope in `~/.claude/CLAUDE.md`, where it loads into every session anyway (see `CONVENTIONS.md` §2–3). Duplicating it per project costs context in every session and creates two copies to keep in sync

Read `examples/claude-md.md` for a reference example before drafting.

---

## PHASE 2 — BUSINESS BRAIN

Identify context needed across multiple skills. Extract into shared files:

```
context/
  brand-voice.md     (tone, style rules, what to avoid)
  audience.md        (who they are, their language, their problems)
  positioning.md     (what makes this distinct, key messages)
  tools.md           (platforms, integrations, credentials location)
```

Rules: 50–100 lines max per file. Skills reference these at the right step — not loaded globally.

If user has an existing doc (brand guide, positioning), ask for the 2–3 most relevant sections rather than the whole file.

Read `examples/claude-md.md` for a reference audience.md before drafting. Confirm breakdown before creating files.

When authoring `context/tools.md`:
- For each external service in the stack, check whether a vendor MCP server exists — record it as the preferred integration path; SDKs are fallback only.
- For each CLI the project depends on, record the installed version + architecture (e.g. `uv --version`) in the prerequisites checklist. This anchors future debugging.

---

## PHASE 3 — FOLDER STRUCTURE

Propose how to split context across subfolder `CLAUDE.md` files. For each, explain what it contains and why it belongs there not in root.

Example: `/content/CLAUDE.md` → tone + format; `/site/CLAUDE.md` → tech stack + patterns; `/email/CLAUDE.md` → platform specifics.

Get confirmation before proceeding.

---

## PHASE 4 — PERMISSIONS

Ask what Claude should auto-allow, what needs approval, what's always denied. Suggest sensible defaults for the project type.

Two key rules:
- **CLAUDE.md instructions are followed ~70% of the time.** For rules that must never break (don't touch .env, don't push to main), use settings.json deny entries — that's deterministic, not advisory.
- **Never use `Bash(git:*)`** — too broad; allows destructive operations. Use granular per-command allows instead.

Read `examples/settings.md` for a full settings.json and explanation. Write to `.claude/settings.json`. Show before saving.

See `CONVENTIONS.md` at the plugin root (§7) for the blocklist of banned permission patterns and the reasoning behind each — never add those to settings.json regardless of what a tool or skill requests.

---

## PHASE 5 — SKILLS

Ask what recurring work I'll do. For each skill, invoke the `build-skill` skill — it handles the discovery questions, authoring rules, and file structure.

**Exception:** if the planning phase already produced clear per-skill scopes (trigger, process, negative trigger, verification all defined), draft each SKILL.md directly using the build-skill authoring rules as a mental checklist. Reserve `build-skill` invocation for genuinely underspecified skills where discovery questions are still open.

Read `examples/skill.md` for the skill chain handoff format if skills need to feed into each other.

---

## PHASE 6 — SKILL CHAINING

Ask whether any skills feed into each other. Document the handoff format: what does upstream output, what does downstream expect? Each skill gets a clean condensed handoff, not the full session context.

Read `examples/skill.md` for a chain example.

---

## PHASE 7 — SLASH COMMANDS

Ask what recurring triggers I want shortcuts for. Create in `.claude/commands/`.

Rules:
- Only create `/project:wrap-up` if it adds something beyond invoking the skill (e.g. pre-flight steps, a reminder). A 3-line wrapper that just calls the skill is noise — skip it and let the user invoke the skill directly.
- Commands are wrappers only — 5–10 lines max. No logic. No decision trees. If it's growing, move logic to a skill.
- Commands never have their own learnings files

Read `examples/commands.md` for format and rules.

---

## PHASE 8 — WRAP-UP SKILL + COMMAND

Wrap-up has its own logic, so it's a skill with a wrapper command — same pattern as every other skill.

Read `templates/wrap-up.md` and create all three files from it:
- `.claude/skills/wrap-up/SKILL.md`
- `.claude/skills/wrap-up/learnings.md`
- `.claude/commands/wrap-up.md`

---

## PHASE 9 — AGENTS

Ask whether the project needs specialist agents. Rule of thumb:
- **Single session**: one focused task, linear work → no agent needed
- **Sub-agent**: self-contained task, runs unsupervised, reports back → define an agent
- **Agent team**: agents need each other's context directly → agent team

For tasks with separable research + implementation parts, no formal definition needed — append *"use subagents"* to the prompt.

For each agent needed, invoke the `build-agent` skill — it handles the authoring rules, decision guide, and verification.

---

## PHASE 10 — REVIEW & CREATE

Show the full proposed structure before creating anything:

```
.claude/
  settings.json
  commands/
    wrap-up.md           ← wrapper (5 lines)
    [other commands]     ← all wrappers
  agents/
    [any agents]
  skills/
    wrap-up/
      SKILL.md
      learnings.md
    [skill-name]/
      SKILL.md
      learnings.md
      checklist.md        ← if relevant
      examples/
        good-example.md   ← if available
context/
  brand-voice.md
  audience.md
  [other shared context]
CLAUDE.md
[subfolder CLAUDE.md files]
```

Before creating, do one quality pass on all proposed skill and agent descriptions: does each have a negative trigger? Does each skill have a verification step? If not, fix before creating — it's harder to retrofit later.

Get approval, then create everything in one pass.

## Verification

After creating, run a grep audit to confirm no misses slipped through:
```bash
grep -rL "Do NOT use" .claude/skills/*/SKILL.md
grep -rL "Verification" .claude/skills/*/SKILL.md
```
Both should return nothing. Fix any that appear before presenting as done.

---

## GUIDING PRINCIPLES

Apply throughout every phase:

- **Context is milk** — fresh and condensed. Load what's needed, release it, don't let it accumulate.
- **CLAUDE.md = context. Skills = process.** Never mix them.
- **Business brain = single source of truth.** Never duplicate context across files.
- **Settings.json = hard rules. CLAUDE.md = guidance.** Critical rules go in settings, not prose.
- **Every mistake becomes a rule.** Learnings files make this self-improving — but only if skills *read* them before running. A growing Staging section is a sign promotion isn't happening.
- **CLAUDE.md is updated three ways**: (1) immediately when a mistake is observed; (2) at every wrap-up; (3) via promotion from learnings. Never defer a known error.
- **Lean beats long.** A focused 80-line file outperforms a 400-line one every time.
- **When in doubt, split** — into a reference file, subfolder CLAUDE.md, or separate skill.

---

## Final step — Capture learnings

After setup is complete, ask:
- Did any phase feel unclear, slow, or produce the wrong output?
- Was anything missing that would have helped?

If yes, append a dated entry under `## Staging` in `learnings.md`:
```
### [Date] — [Project name or type]
- What caused friction: ...
- What worked well: ...
- Suggested change: ...
```

Then apply promotion + pruning:
- If the issue is likely to recur → edit `SKILL.md` immediately; delete the Staging entry
- If one-off → leave in Staging only
- If older than 30 days unpromoted → delete
