# Best-in-class app-craft checklist

The patterns that separate software that *works* from software that feels *world-class*. Walk
every rendered surface against these. A pattern being absent is not automatically a finding —
judge whether its absence is felt on THIS app's surfaces. Flag what's worth changing; say when
something already works and move on.

Severity: **priority** (actively undermines the feel; a first-time user notices) · **notable**
(worth the next fix pass) · **polish** (small, low urgency). A TASTE finding (subjective design
judgment) carries that label and a screenshot-anchored reason, never a bare "restyle it".

---

## 1. Identity & chrome
- **Per-route page titles.** Does the browser tab / history / bookmark say where you are, or the
  same app name everywhere? (Next App Router: `metadata`/`generateMetadata` per route.)
- **Favicon set**, not just a lone `.ico`: `icon`, `apple-touch-icon`, and a maskable icon so
  Add-to-Home-Screen looks intentional.
- **Web app manifest** (name, icons, `theme-color`, `display`) — the difference between a
  bookmark and something that opens like an app on a phone.
- `<html lang>` set; `<meta name="theme-color">` matched to the chrome.
- Open Graph / Twitter card on any publicly-shared route (a landing page especially).

## 2. Navigation & orientation
- **Sticky / condensing header** on long-scroll pages — can the user navigate without scrolling
  back to the top? (Judge per page: a short form doesn't need it; a day-long dashboard does.)
- **Current-route indication** — does the nav show where you are?
- **Scroll behavior**: position restored on back-nav; deep-links scroll to and highlight their
  target; a newly-revealed panel scrolls into view rather than opening off-screen.
- Logo/home affordance always returns home.

## 3. Loading & perceived performance
- **Long operations (>1s) get real feedback** — a skeleton or an *animated* indicator, not
  static text that reads as frozen. The longer the wait, the more this matters (an LLM call is
  the acid test).
- **Skeleton screens** over spinners where the layout is known — they reduce perceived wait and
  prevent layout jump.
- **No layout shift (CLS)**: images/media carry width+height or an aspect-ratio box; late-loading
  content reserves its space. Unsized `<img>` on a landing page is the classic offender.
- **Font loading**: a web font needs `font-display: swap` and ideally preload; a system-font
  stack sidesteps this entirely (and is a legitimate best-case, not a gap).
- Optimistic UI on confirmable actions — the change appears instantly, reconciles on response.

## 4. Interaction craft & motion
- **Transition discipline** — state changes (hover, open, expand) ease rather than snap; motion
  is short (~0.12–0.2s) and cheap (opacity/transform). Too much is as bad as none.
- **Every interactive element has hover AND active AND focus feedback** — nothing dead to the
  touch.
- **`prefers-reduced-motion`** respected wherever there's animation.
- Micro-interactions that reward without distracting (a checkmark settling, a row fading on
  complete) — taste, not obligation.

## 5. Input & accessibility
- **Keyboard reachable end-to-end**: tab to every control, visible **`:focus-visible`** ring on
  each (not just form inputs; buttons and custom controls too).
- **Escape closes every modal/popover/overlay**; Enter submits the obvious form.
- **Touch targets ≥44×44px** (Apple HIG) — the smallest nudge arrows, icon buttons, and info
  triggers are where apps fail this.
- **Skip-to-content link** and an `sr-only`/visually-hidden utility for screen-reader labels.
- **Contrast** meets WCAG AA (4.5:1 body, 3:1 large/UI) — muted-grey-on-cream is the usual miss.
- Form fields have real labels (placeholder-only vanishes on type).

## 6. States
- **Empty states** teach and invite ("nothing yet — here's how to start"), never a blank void.
- **Error states are visible** — a failed fetch must SAY so, never silently render as empty
  (an empty week that's actually a network failure is a trust-killer).
- Offline / retry affordance on anything network-dependent.
- Destructive actions confirm; irreversible ones say so.

## 7. Mobile app-ness
- **Add-to-Home-Screen** produces a real app icon + name (needs manifest + apple-touch-icon).
- **Safe-area insets** (`env(safe-area-inset-*)`) so fixed elements clear the notch / home bar.
- Momentum scroll doesn't trap; fixed elements don't cover content or each other.
- Tap highlight / active states tuned for touch (no lingering hover state stuck on).
