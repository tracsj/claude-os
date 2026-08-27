# Worked example

Two parts: a full worked shaping doc for a small fictional app (to calibrate tone and depth),
and a real door-triage illustration drawn from an actual project's history (to show *why* the
door sort is the whole game).

---

## Part 1 — A full shaping (fictional: a reading-list app)

A single-user app that saves articles to read later and nudges the ones going stale.

```markdown
# SHAPING — Readpile

_Living design. Top sections = current shape; dated sections below = changelog._

## Appetite
A weekend. If it doesn't fit a weekend, cut the nudging, ship the saving.

## The job
Someone finds an article mid-day, has no time now, and wants it to resurface later without
becoming a guilt pile of 400 open tabs.

## Data model
- **Article** — a saved URL; owns title, url, savedAt, readAt (null until read), source.
- **Tag** — a label; many-to-many with Article.
- Source of truth: read/unread state is the single field `readAt` on Article (a null vs. a
  timestamp), never a separate boolean — one fact, one place.

_Invariants live in CLAUDE.md. Link: CLAUDE.md ## Always → "readAt is the only read-state
signal; a boolean `isRead` is banned (it double-sources the fact)."_

## Vocabulary
- **save** — add an article (never "bookmark", never "add")
- **stale** — unread ≥ 14 days (the only definition; the nudge and the filter both use it)
- **pile** — the full unread set

## Vertical slice
Save one article from a paste box → it appears in the pile → mark it read. Data → API → UI,
end to end. Proves the store shape and the read-state model before any tagging or nudging.

## Deferring for now
- Browser extension (two-way door; the paste box proves the flow first) — revisit after slice.
- Tag UI — the Tag entity exists in the model, but no tagging screen until the pile works.
- Multi-user / auth — single-user local until the core earns it.
```

Notice what got the up-front thought: the **read-state source of truth** (one-way door — a
`readAt` timestamp vs. a separate `isRead` boolean is a schema decision that ripples), the
**"stale" definition** (vocabulary — pinned once so the nudge and the filter can't diverge),
and the **slice**. The deferrals are the other half: the extension, the tag *screen*, auth —
all two-way doors that cost nothing to decide later.

---

## Part 2 — Why the door sort matters (a real project's history)

A daily stand-up assistant I built — an app that reads a person's inbox and calendar and produces
a morning brief — kept a `CLAUDE.md` that reads like a ledger of which doors turned out expensive
and which turned out cheap. It's the clearest argument for this skill:

**Expensive (one-way doors that rippled when reversed):**
- **`urgency` derived → stored.** Urgency started as a computed value, then became a stored
  1–5 field. Reversing it touched extraction, the scoring guidance, the stand-up's reasoning,
  and the store schema — because it's a *data-model* decision, and every feature coupled to it.
- **Flat-file calendar → Google Calendar as source of truth.** Moving the authoritative store
  of "what's on the calendar" cascaded through scheduling, free-window computation, the day
  timeline, and every read path. A source-of-truth change is the most expensive door there is.

**Cheap (two-way doors, reversed with little cost):**
- Retiring the email-preview page.
- Merging a separate check-in modal and an inline refine loop into one chief conversation.

Both categories are normal product evolution. But the expensive ones are exactly the decisions
a 20-minute shaping pass would have surfaced as one-way doors worth an extra hour of thought —
*is urgency a stored fact or a derived one? which system owns the calendar?* — while the cheap
ones were fine to discover by building.

Where that project did well without a formal ritual: its **vocabulary held steady**. The four or five
domain nouns it started with were still the nouns a year later, which is why its features compose
instead of fighting each other. That steadiness is the vocabulary invariant paying off, and
it's exactly what Phase 1 pins on purpose.
