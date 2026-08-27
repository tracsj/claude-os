# Skill Examples

## Good SKILL.md (newsletter writing)

```markdown
---
name: newsletter
description: Write the shop's weekly newsletter. Use when asked to
draft, outline, or write the newsletter, or when user says "newsletter".
Do NOT use to repurpose newsletter content for social media — use the
repurpose skill instead.
---

Before starting, read `.claude/skills/newsletter/learnings.md` and apply any guidance from the Staging section.

# Weekly Newsletter Skill

## Overview
Produces a 400–600 word newsletter in a bookseller's voice. Warm,
direct, one book properly recommended per issue. Never promotional.

## Process

### Step 1 — Understand this week's topic
Ask me: what's the main idea, story, or question for this issue?
If I have rough notes, read them now.

### Step 2 — Load audience context
Read @context/audience.md
Read @context/brand-voice.md

### Step 3 — Draft structure
- Opening hook (1–2 sentences, a story or provocative question)
- The core idea (the insight or reframe)
- Practical application (what the reader can do today)
- Closing line (warm, not salesy)

### Step 4 — Write the draft
Follow the structure above. Stay under 600 words.
Reference @skills/newsletter/examples/good-issue.md for tone calibration.

### Step 5 — Self-review before showing me
Check against @skills/newsletter/checklist.md
Flag anything that feels corporate, vague, or too long.

### Step 6 — Present and await feedback
Show the draft. Ask: anything to adjust before we finalise?

## Common Mistakes to Avoid
- Starting with "In today's issue..." — too formulaic
- Ending with a hard sell — always a soft, optional CTA
- Using more than one exclamation point per issue
- Summarizing rather than saying something

## Reference Files
- examples/good-issue.md — annotated example of a strong issue
- checklist.md — pre-send review checklist

## Final step — Capture learnings
If anything worked particularly well or went wrong, append a dated entry
under `## Staging` in `.claude/skills/newsletter/learnings.md`. Apply promotion + pruning:
- Likely to happen again → promote into this skill file; delete the Staging entry
- Repeated 3+ sessions → same
- One-off → leave in Staging only
- Older than 30 days, unpromoted → delete
```

---

## Skill chain example (content business)

```
content-strategy skill
  → outputs: topics.md (title, angle, platform, target date)

newsletter skill
  → reads: topics.md at Step 1 if no topic given by user

repurpose skill
  → reads: final newsletter draft
  → outputs: LinkedIn post, 3 tweets, one short-form idea
```
