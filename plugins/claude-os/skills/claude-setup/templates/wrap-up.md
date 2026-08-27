# Wrap-up — templates

Create these three files for every new project.

---

## `.claude/skills/wrap-up/SKILL.md`

```markdown
---
name: wrap-up
description: End-of-session capture. Summarizes session, captures learnings for each skill used, applies promotion + pruning, confirms CLAUDE.md accuracy. Do NOT use for mid-session notes, for a running commentary, or as a substitute for fixing a problem the moment it is found — a known error is never deferred to wrap-up.
---

Before starting, read [wrap-up learnings](learnings.md) and apply any guidance from the Staging section.

Before we close this session:

1. **Session summary** — 3–5 bullets on what we did today.

2. **Permissions audit** — Read `.claude/settings.json`. Flag any allow rules matching banned patterns: `node:*`, `python3:*`, `curl:*` (unscoped), `rm:*`, or any `*:*` wildcard. Remove or tighten before proceeding. Takes 10 seconds; prevents security regressions persisting across sessions.

3. **Learnings** — For each skill used, check if anything worked particularly well or went wrong. Either write a dated entry under `## Staging` in `.claude/skills/<name>/learnings.md` OR explicitly confirm nothing to capture. Then apply promotion + pruning:
   - Likely to happen again → promote into skill file or CLAUDE.md; delete the Staging entry
   - Repeated 3+ sessions → same
   - One-off → leave in Staging only
   - Older than 30 days, unpromoted → delete
   Staging stays lean — a handful of active observations at most.

4. **Files** — Flag any file over 150 lines and suggest how to split it.

5. **Stale working-doc scan** — Run `find . -maxdepth 1 -name "*.md" | sort`. Known-persistent (skip): `CLAUDE.md`, `README.md`, `CHANGELOG.md`, `CONTRIBUTING.md`, `LICENSE.md`. For each file not on that list, ask the user: delete, move to the right subfolder, or keep with an explicit note added to CLAUDE.md. Do not silently skip or auto-delete.

6. **Context file descriptions audit** — for any file added to or updated this session, check that its description in the parent CLAUDE.md names the sections now inside it. If you find one stale or missing description, check siblings — staleness clusters. Descriptions should be section-aware, not just topic-level.

7. **CLAUDE.md accuracy** — Three passes:
   - *Additions*: anything new this session that should be documented?
   - *Staleness*: is everything already there still accurate?
   - *Global*: did anything reveal a universal principle? Before adding to `~/.claude/CLAUDE.md`, pass the 3-question filter in `CONVENTIONS.md` §8 — all three must pass, and if they do not, route the rule to skill gotchas, this project's CLAUDE.md, or a reference file instead.
   - *Size and shape*: that filter governs *whether* a rule is admitted and says nothing about how many words it brings with it. Both are failure modes, and the second is the one nobody notices: a hub file made entirely of correct rules becomes unusable if each arrives with its full incident narrative attached. Keep the action in the hub and move the justification, the mechanism and the tell to a spoke file. If you want this enforced rather than remembered, write a check that measures median words per edit against a healthy sibling file and run it here.

8. **Next-session brief** — update the project's live working doc so a fresh session is not blind. For a small project that is a `## Where we are` section at the bottom of root `CLAUDE.md`; for a larger one it is `Handoff.md`, `PROGRAMME.md` or whatever the project already keeps. Whichever it is, root `CLAUDE.md` must point at it explicitly, since an approved plan sitting in `~/.claude/plans/` under a generated name is not a handoff and no future session can guess which one belongs here. The brief must name a **concrete next action and the file it touches** — "continue where we left off" is not a brief.

9. **Memory health** — Periodically (every 5–10 sessions), run whatever cross-skill audit you keep: consolidate learnings across skills, prune stale Staging entries, check memory integrity. Skip if the last run was recent. (This plugin does not ship an audit skill — see `CONVENTIONS.md`, *What this plugin does not ship*. Drop this step if you have not built one.)

10. **Commit wrap-up changes** — Stage and commit any changes made during wrap-up (learnings, CLAUDE.md, memory files). Run `git status` in the project repo to confirm it is clean. If you also keep `~/.claude/` under version control, run it there too — a session that edits a user-scope skill or CLAUDE.md has touched a second repository, and a clean project repo says nothing about that one.

## Verification

Run `git status` in both repos and `git log --oneline -3` in the project repo as the very last action, then output this report with each step marked ✓ or ✗:

```
Wrap-up complete:
✓/✗ Step 1 — Session summary (N bullets)
✓/✗ Step 2 — Permissions audit (clean / fixed N rules)
✓/✗ Step 3 — Learnings (skills: X, Y — entries written / nothing to capture)
✓/✗ Step 4 — Large files (clean / flagged X)
✓/✗ Step 5 — Stale working-doc scan (clean / deleted or relocated N)
✓/✗ Step 6 — Context file descriptions audit (clean / updated N)
✓/✗ Step 7 — CLAUDE.md (N additions / nothing stale / global check)
✓/✗ Step 8 — Next-session brief written (names action + file)
✓/✗ Step 9 — Memory health (skipped / ran the cross-skill audit)
✓/✗ Step 10 — Changes committed; git status clean (project + ~/.claude)
```

If any step is ✗, state why before closing.

## Final step — Capture learnings
If a wrap-up step behaved unexpectedly — skipped, fired incorrectly, or a pattern recurred — append a dated entry under `## Staging` in `learnings.md`. Apply promotion + pruning per root CLAUDE.md rules.
```

---

## `.claude/skills/wrap-up/learnings.md`

```markdown
# wrap-up skill — learnings

## Staging
```

---

## `.claude/commands/wrap-up.md`

```markdown
---
description: End-of-session capture. Run before closing any session.
---

Before starting, read `.claude/skills/wrap-up/learnings.md` and apply any guidance from the Staging section.

Run the wrap-up skill at @.claude/skills/wrap-up/SKILL.md.
```
