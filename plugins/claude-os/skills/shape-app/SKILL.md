---
name: shape-app
description: Shape an application's data model and core flow before building features — front-loads the hard-to-reverse decisions (schema, entity relationships, source-of-truth, domain vocabulary) and writes a living SHAPING.md. Use when starting a new app, or before a feature that adds or changes an entity, relationship, or source of truth, or when asked to architect / shape / design something before writing code. Do NOT use for small tweaks, copy changes, or bug fixes that touch no data (there is nothing to shape), and do NOT use to scaffold the .claude/ folder — use claude-setup for that.
---

Before starting, read [shape-app learnings](learnings.md) and apply any guidance from the Staging section.

# Shape App

Front-loads the decisions that are expensive to reverse, and *only* those, before code gets
written. The output is a `SHAPING.md` at the repo root and a few seeded rules in the project
`CLAUDE.md`. It exists to catch the one-way-door mistake while it is still cheap.

---

## When to use

Run the **mechanical door test** first — this replaces any "is this big enough?" guessing:

> Does this work add or change **a new entity, a new relationship, a new source-of-truth, or
> a new vocabulary term?**

- **No** → skip the skill. A copy change, a style tweak, a bug fix, a new screen over an
  existing model touches only two-way doors. Say so and move on. Do not railroad a tweak into
  a ritual.
- **Yes** → shape it. Pick the tier by how much is new:
  - **Small tier** (one new entity or field, an added relationship): answer **three questions**
    — What new entities/fields? What must always be true about them (invariants)? What's the
    one slice I'll build first? Record the answers as a dated changelog delta in `SHAPING.md`;
    any new build-time invariant still goes to `CLAUDE.md` per Phase 5's sort, with the delta
    linking to it. No `SHAPING.md` yet? Then this is the project's first shaping — run the full
    tier so the base sections exist.
  - **Full tier** (a new app, or a feature that reshapes the model): run Phases 0–5 below.

The failure mode this skill has to beat is **dying after one use** — a template nobody opens
at feature N. Two things prevent that: the mechanical gate above (so it's obvious when to
skip), and Phase 5 seeding a rule into `CLAUDE.md` that re-summons the skill on its own.

---

## The core move

Sort every decision into **one-way doors** (hard to reverse — data schema, entity
relationships, which store owns a fact, the domain vocabulary) and **two-way doors** (cheap to
reverse — screen layout, copy, feature order, styling). Spend the up-front thinking budget on
the one-way doors and *deliberately defer* the two-way ones.

Revision is normal, and discovering the problem by building it is legitimate. The aim is
making the expensive revisions rare while keeping the cheap ones cheap.

---

## Process (full tier)

### Phase 0 — Reconcile with what already exists *(re-runs only)*
If a `SHAPING.md` already exists, read it first and ask the question that matters most on a
re-run: **does this feature bend an existing invariant, or add a SECOND source of truth for a
fact that already has one?** That is the expensive mistake — catch it here. Also scan for
existing design docs (`context.md`, `docs/*-design.md`, an existing `CLAUDE.md`) and integrate
with them; do not plant a parallel doc that drifts.

**Triaging "reuse the existing module vs build new" in an LLM app: read its PROMPT and its
PERSISTENCE before its exports.** Reusability is decided there, not by the function signature — an
LLM module's args are usually null/empty-safe, so a signature-level check says "reusable" and type-
checks fine while the *prompt* quietly forbids the thing you need (e.g. "keep the proposed arrays
empty on the opening turn" → your first capture turn returns nothing) and the *route* writes to a
doc you'd clobber. A null-safe signature over a ritual-shaped prompt is a trap that looks like a
green light. The cheap reuse is usually one layer down: the module that isn't coupled to the ritual,
only *anchored* to one framing in its wording.

**When Phase 0 finds the door already deferred, read the deferral's REASON before its scope.** A
deferral records why something looked expensive *at the time*, and that reason is itself a claim
that can expire. The cheap outcome is often a constraint you can decline to take on rather than a
cost you have to absorb. Two shapes recur. A reason of the form *"this would make X a second source
of truth"* describes a consequence of a design choice, not a property of X — deciding the app never
claims to know X's current state can dissolve it. A reason of the form *"X is unreachable"* is a
**factual claim about a tool surface, and it is the one to probe rather than re-read** — surfaces
change, and the doc does not. **Probe it with a control**: a positive result alone can mean the
filter was ignored, so pair it with a query you expect to come back empty and check that it does.
*(Both shapes turned up in one afternoon on an email-triage app. One deferral dissolved once the app
stopped claiming to know a calendar's current state; another called a mail folder unreachable, and
a probe found it readable — but only the empty control proved the filter was real rather than a
silent alias for a different folder.)*

### Phase 1 — Data model + invariants
Start by pinning **the job** (who it's for, what they're trying to do) and **the appetite** (how
much time it's worth) in one sentence each — they cap the scope. Then name the nouns and their
relationships, not the screens. For every core fact, name its **source of truth** — one place,
or a deliberate, documented split with a rule for reconciling them (two on purpose is fine when
it's named; unnamed drift is the bug). **When a new fact could join an existing append-only log, ask what the log's FOLD does with it —
not whether it fits the schema.** A log's contract is its reducer, not its columns. An event can
validate perfectly inside a log and still be wrong there, and the failure is silent and permanent:
every future replay steps over a line it must ignore. The tell is a new event type that no existing
`case` in the reducer would ever match — if the answer to *"what does replay do with this?"* is
*"nothing"*, it belongs in its own store. This question separates stores far more reliably than
data shape does. *(Found in an app carrying three append-only logs — one folded into item state,
one never read by code at all, one folded to answer a different question entirely. Three identical
shapes, three different contracts.)*

Then write the **invariants** — the things that must always be true ("a
commitment belongs to exactly one matter"; "raw email bodies are never persisted"). Hold the
**domain vocabulary** fixed — one word per concept, used everywhere.

### Phase 2 — Breadboard the flow to stress-test the schema
This phase stress-tests the schema; screens and flows stay deferred (two-way doors). Walk the
user's job end-to-end as **places → things → connections** (boxes and arrows, no visual design)
to surface **what the schema forgot** — a missing entity, field, or state that only shows up
when you trace the job.

### Phase 3 — Vertical slice
Name the **one** feature to build end-to-end first (data → API → UI → the rendered thing you
look at). It validates the architecture while a mistake costs one refactor, not twelve. Breadth
comes after the slice holds up.

**State the slice as a pair: the thing working, AND the thing failing when its one precondition is
removed.** A happy-path slice that passes first time proves far less than it looks — it cannot
distinguish "the mechanism works" from "nothing was ever going to break here." Build the broken
version deliberately, watch it destroy what it should destroy, then fix it and re-run. That is also
how you find out whether an existing guard covers the new failure, which is usually the cheapest
place to put the fix. *(On one build, the slice "a deferred item survives being snoozed" passed
immediately and proved nothing. Deliberately dropping the scope clause then destroyed a live item
while the existing guard stayed silent, because one bad retirement in eight is not conspicuous
enough to trip anything. The guard that came out of it was the actual deliverable, and the happy
path would never have asked for it.)*

### Phase 4 — Deferral list
Write down what you are choosing **not** to decide yet — the two-way doors left open on
purpose. This is what stops over-designing; most docs record only decisions and over-commit.

### Phase 5 — Write it down, seed CLAUDE.md, seed the re-trigger
1. Instantiate or **update** `SHAPING.md` from `templates/shaping-doc.md` — base sections are
   living (edit them when a feature changes the shape), later features append a dated changelog
   delta. See the template. **Edit the base in the SAME pass as the delta, never later.** A delta
   is not a substitute for the base, and the contradiction between them is invisible until someone
   reads the doc top-down — which is what a project's `CLAUDE.md` usually tells the next session to
   do. The concrete smell: a delta announcing a new entity sitting under a Data model that still
   counts the old number. *(Two occurrences: 2026-08-26 a rival source of truth hidden in prose,
   2026-08-27 a base left stale while the delta was current.)*
2. **Sort the invariants into two bins** — do not put them in two places:
   - **Build-time constraints** → the project `CLAUDE.md` `## Always` / `## Never`. This is the
     *canonical* home for invariants (loaded every session, actually enforced). Never also
     store them in `SHAPING.md` — that's the two-source-of-truth trap.
   - **Runtime data constraints** ("an order always has a customer") → types / validation in
     code, NOT `CLAUDE.md`, or the rules file bloats with pseudo-rules.
3. **Seed the re-trigger** into `CLAUDE.md`: a rule like *"Before building a feature that adds
   or changes an entity, relationship, or source of truth, run `/shape-app` (three bullets is
   enough)."* This is what makes the skill re-summon itself at feature N.
4. If the project has no `.claude/` folder yet, point to **`claude-setup`** to scaffold it.

---

## Verification

Before calling a shaping done, confirm the doc actually did its job — an under-considered doc
is the real risk:

- [ ] Every **one-way door** in scope is named (entities, relationships, source-of-truth,
      vocabulary) — not just the features.
- [ ] Each core fact has **one** source of truth — or a deliberate split with a named
      reconciliation rule (two sources on purpose is fine when named; unnamed drift is the bug).
- [ ] There is a **deferral list** (what's deliberately not decided).
- [ ] There is **one vertical slice** named.
- [ ] Invariants are sorted into CLAUDE.md-bound (build-time) vs types-bound (runtime), living
      in **one** place each.
- [ ] The re-trigger rule is in `CLAUDE.md` (so feature N re-summons this).
- [ ] The ceremony matched the size — a small change got the three-question path, not the full
      five phases.
- [ ] **Every consumer of a shared schema is verified — scaled to what changed.** List them first
      (one definition often feeds several input paths). A **structural** change (field added or
      removed, `required` altered) → run EVERY consumer live, the strictest (`strict: true`) first,
      since a strict round-trip can break. A **description reword** → find which consumers' prompts
      already **override** that field (their instruction wins, so the description was never
      load-bearing for them) — then run them anyway if it's cheap; a lib function is usually
      side-effect-free and callable from a scratch script even when its route is not.
- [ ] **A new control is REACHABLE, which is a separate question from whether it works.** Route
      tests and unit tests both pass on a control no user can get to. Ask what state the app is in
      when the control is needed, then navigate there the way a person would — the answer is often
      that the surface hosting it has already gone. *(A date field for rescheduling was added to a
      card and verified at the route level. Deciding an item settles it, the main view filters
      settled items out, so the card vanished at the exact moment the control became relevant. Only
      clicking through found it; it moved to the list view, the one surface left.)*
- [ ] **A stochastic field that degrades QUIETLY is distribution-verified, not point-verified** —
      if a miss falls back to something that still looks intentional (a plausible "New" pill, a
      generic label), n=1 cannot see it. Sample the constrained end of the input range, not the
      typical middle, and report the rate.

---

## Reference

- `templates/shaping-doc.md` — the `SHAPING.md` body to instantiate (living base + dated
  changelog). Read and copy it.
- `examples/worked-example.md` — a worked shaping + a real door-triage illustration. Read for
  calibration before writing.

---

## Final step — Capture learnings

If anything worked particularly well or went wrong, append a dated entry under `## Staging` in
`learnings.md`. Also ask: **did anything this session make `templates/shaping-doc.md` or
`examples/worked-example.md` stale?** If yes, update that spoke file now — consumption without
maintenance is silent drift. Apply promotion + pruning per `CONVENTIONS.md` §3:
- Likely to happen again → promote into this SKILL.md or the spoke file; delete the Staging entry
- Repeated 3+ sessions → promote
- One-off → leave in Staging only
- Older than 30 days, unpromoted → delete
