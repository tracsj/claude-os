---
name: app-ux-audit
description: Audit a web app against best-in-class application-CRAFT patterns — the details that make software feel world-class rather than merely correct (sticky headers, loading/skeleton states, per-route titles, focus rings, touch targets, motion discipline, error/empty states, PWA chrome). Report-only by default; fixes are a separate gated pass. Invoke for "UX audit", "app polish", "does this feel world-class", "craft review". Do NOT use for brand/visual redesigns (that's a project's own design skill), for audience-comprehension reviews (e.g. "would the user understand this?" — that's a project review skill), or for content/copy edits.
---

Before starting, read this skill's `learnings.md` and apply any guidance from the `## Staging` section.

# App-UX Craft Audit

## What this is (and is not)
This audits **craft** — the interaction, chrome, performance, and accessibility patterns that
make an app feel world-class. It is NOT a brand/visual redesign (a project's design system owns
that) and NOT a comprehension review ("does the target user understand the screen?" — a
project-specific review skill owns that). Those are different lenses; keep them separate, and say
so if a request is really one of the others.

## Posture
A senior product-craft designer forming a **view**, not generating a to-do list. Be direct about
what isn't landing. Only flag what's worth changing — if a surface already works, say so briefly
and move on. Restraint that reads as elegance is a finding worth *praising*; restraint that reads
as unfinished is a finding worth raising. Subjective design judgments carry a **TASTE** label and
a screenshot-anchored reason, never a bare "make it nicer".

## Process

### 1 — Orient
Read the project's `CLAUDE.md` (register, stack, audience) and this skill's `learnings.md`. State
which surfaces you'll cover. Start the dev server if it isn't up.

### 2 — Rendered surfaces first (the core discipline)
**Screenshot every surface at desktop AND mobile before reading source.** Craft findings —
motion, density, proportion, does-this-feel-frozen — live in the render, not the code. Code is
secondary: use it to explain *why* something renders as it does, never to substitute for seeing
it.
- **Mobile mechanics**: headless Chrome silently enforces a ~500px minimum window width and CROPS
  below it (manufacturing false overflow bugs). Verify ≥500px via headless; narrower via a
  container-clamp + measurement in a real tab (`getBoundingClientRect`, `scrollWidth >
  clientWidth`); anything that needs a true device (safe-area insets, real touch, PWA install)
  goes in an explicit **"verify on device"** report section — never silently skipped.
- Progressive-reveal surfaces (IntersectionObserver fades, lazy content) need progressive
  scrolling before the screenshot; animated elements need their rest state forced before judging.

### 3 — Walk the checklist
Assess every surface against `references/checklist.md` (7 sections: identity/chrome, navigation,
loading/perf, interaction/motion, input/access, states, mobile app-ness). Verify **mechanical**
facts by grep (a `metadata` export exists or doesn't; a `position: sticky` is there or not) —
those are reliable. Never file a **rhythm / proportion / feel** finding from a code read or an
agent summary alone; those over-claim. Re-verify any inherited inventory claim against the live
render before filing it.

### 4 — Report (report-only session)
Write a severity-ranked report to `docs/ux-audit-<date>.md` in the project. **No fixes in the
audit session** — the report feeds a separate implementation pass so the operator chooses what
ships. Severity: **priority** (undermines the feel, a first-timer notices) · **notable** (next
pass) · **polish** (low urgency). Every finding carries render/measurement evidence, not just a
file:line. Include a "verify on device" section for what local tooling can't prove.

For a large app, fan out lens agents (interaction+motion, access+input, chrome+perf, design
taste) — each instructed to verify against renders and re-check inherited claims, not trust them.
Synthesize; dedupe; rank.

### 5 — Fix pass (separate, gated)
Only after the operator picks findings. Per fix: **re-verify the finding against current code
first** (punch lists go stale — items get fixed in intervening sessions), apply, **render-verify
the result** (tsc passing ≠ it reads right), and **grep the full surface before standardizing any
detail app-wide** (consistency drifts are invisible one-surface-at-a-time — land one coordinated
pass, not per-page patches). Then capture learnings, update the project's docs/invariants, commit.

## Gotchas
- **Render, don't reason from spec.** Sub-pixel hairlines, color absorption, a `var()` falling
  back to transparent, elements colliding — visible only at rendered scale. If the env can't
  reproduce real conditions, get a real screenshot before calling a visual finding done.
- **Mechanical grep = reliable; "rhythm"/sequence from a summary = not.** Explore/agent summaries
  over-claim structural understanding and produce confident false positives on
  proportion/sequence. Read the canonical source or the render directly for any non-mechanical
  claim.
- **Mobile-breakpoint blind spot.** A narrowed desktop window is not a device — filter-heavy CSS
  can render fine on desktop and break on mobile GPUs; touch has no hover. Pixel-check at the real
  breakpoint, and put device-only findings in their own section.
- **Screenshot mechanics.** IntersectionObserver fade-ins block direct-jump screenshots (scroll
  progressively first); animated marks must be forced to rest before you judge them.
- **The absence of a pattern is not automatically a finding.** A short form doesn't need a sticky
  header; a system-font stack legitimately skips `font-display`. Judge whether the gap is *felt*
  on this app's actual surfaces before ranking it.
- **Dev-only overlays render in screenshots but ship to no one.** A Next.js dev-mode indicator,
  React Query devtools, a Vercel toolbar — all appear in captures and none exist in production.
  Before filing any chrome-collision/overlap finding, confirm the element is a real page node
  (`document.elementFromPoint(x,y)` — a dev overlay returns `NEXTJS-PORTAL` or similar, not app
  markup) or check the production build. (An "N" badge filed as a brand-mark collision was the
  Next.js dev indicator.)
- **Verify which element scrolls before filing a scroll-jump/reset.** `window.scrollY` can read 0
  because an inner `overflow:auto` container is the real scroller — measure THAT element's
  `scrollTop` before/after. `scrollY:0` means "the window didn't move," not "jumped to top." (A
  chip that already `preventDefault`s was filed as a scroll-to-top bug purely on a `window.scrollY`
  misread.)

## Verification
Before calling the audit done:
- [ ] Every surface screenshotted at desktop + mobile; findings carry render/measurement evidence.
- [ ] Mechanical claims grep-verified; no feel/proportion finding rests on a code read alone.
- [ ] Report written to `docs/ux-audit-<date>.md`, severity-ranked, with a "verify on device"
      section. No fixes applied in the audit session.

## Final step — Capture learnings
Append a dated entry under `## Staging` in `learnings.md` if anything notable happened (a check
that misfired, a pattern that recurred, a project-specific constraint discovered). Apply promotion
+ pruning per `CONVENTIONS.md` §3 — promote what generalizes into the Gotchas above and
delete the Staging entry; keep Staging a handful of live observations, not an archive.
