# Shaping doc template

Instantiate this as `SHAPING.md` at the repo root. **Two zones:**

- **Base sections are LIVING.** The top of the file always reflects the *current* shape of the
  app. When a feature changes the data model, vocabulary, or slice, you **edit the base
  section** — you do not leave the old version standing.
- **The changelog is a DELTA log.** Each new feature appends one dated section recording only
  *what it changed and why* — not a fresh copy of the whole design. Top of file = current
  truth; dated sections = how it got here.

Strike or mark superseded deltas rather than letting stale ones pile up, so the top of the file
always reads as the current shape.

Build-time invariants have **one** home — the project `CLAUDE.md` (`## Always` / `## Never`);
runtime data constraints live in types/validation. This doc holds the *reasoning*; it may link
to the rules, it must not duplicate them.

A **full** shaping fills the living base sections and adds an opening delta; a **small-tier**
shaping fills only a dated delta — its three answers (new entities/fields, new invariants, the
slice) map onto the delta's fields below.

---

```markdown
# SHAPING — <app name>

_Living design. Top sections = current shape; dated sections below = changelog._

## Appetite
How much time this is worth. (An hour? A weekend? A month?) The appetite caps the scope —
if the shape won't fit the appetite, cut scope, don't overrun.

## The job
Who is this for, what are they trying to get done, and in what state are they when they reach
for it? One or two sentences. (e.g. "A solo lawyer opens it at 9am, anxious that something
urgent is hiding the important thing.")

## Data model
The nouns and their relationships. For each core fact, name its source of truth — one place, or
a deliberate split with a reconciliation rule.

- **<Entity>** — <what it is>; owns <fields>; relates to <entity> by <relationship>.
- **<Entity>** — …
- Source of truth: <fact> lives in <store/system>. (If deliberately split — e.g. an external
  system owns X, the app owns Y — name the split and the rule that reconciles them, e.g. "app
  pins never write back to the external calendar".)

_Invariants (the must-always-be-true rules) live in CLAUDE.md, not here. Link: <path/anchor>._

## Vocabulary
One word per concept, used everywhere. List the terms and what each means, so two parts of the
app never mean different things by the same word.

- **<term>** — <meaning>
- **<term>** — <meaning>

## Vertical slice
The ONE feature built end-to-end first (data → API → UI → the rendered thing) to validate the
architecture before building breadth.

- Slice: <the one thin path>
- It proves: <which architectural assumption it validates>

## Deferring for now
Two-way doors deliberately left open. What we are choosing NOT to decide yet.

- <deferred decision> — revisit when <trigger>
- …

---

## <feature name> — YYYY-MM-DD
_Changelog delta. Record only what changed._

- **Touches:** <which base section(s) this amended — Data model / Vocabulary / slice>
- **New one-way doors:** <new entity / relationship / source-of-truth / term>
- **Invariant impact:** <new invariant added to CLAUDE.md, or existing one bent — and how>
- **Slice:** <the one thing built first for this feature>
- **Deferred:** <what this feature is not deciding yet>
```
