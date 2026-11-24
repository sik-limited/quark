---
name: animate
description: Decide whether and how a web interface should move, then build it with the right easing, duration, spring, choreography, gesture handling, performance and reduced-motion fallback. Use when adding, tuning, reviewing or removing animation, transitions, enter/exit, drag/swipe/sheet behavior, stagger, hover/press feedback, or when motion feels slow, janky or off.
license: MIT
---

# Animate

Use this when a request involves motion of any kind: a new transition, a component that should feel alive, a gesture, a stagger, jank, or "this feels slow". Run the sections in order. Sections 1 and 2 can end the task with zero lines of animation, and that is a valid outcome.

Rules for every section:
- Use the project's existing easing and duration tokens. Extend them, never fork a parallel set. Quark components read `--q-duration`; define the easing tokens below next to it.
- Values here are exact starting points. Do not invent near-misses (`cubic-bezier(0.4,0,0.2,1)` is not `--ease-out`).
- Reduced motion and hover gating ship with the animation, not after it.
- Motion is never the only carrier of a state change. Pair it with a static cue (color, icon, label).

```css
:root {
  --ease-out: cubic-bezier(0.23, 1, 0.32, 1);      /* enter / exit, default */
  --ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);  /* on-screen move / morph */
  --ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);   /* edge-attached sheets and drawers */
}
```

## 1. Gate: how often will the user see this?

| Frequency | Decision |
| --- | --- |
| 100+ per day, or keyboard-initiated (command palette, shortcuts, list navigation by keys) | No animation. Instant state change. Stop here. |
| Tens per day (row hover, tab switch, menu item hover) | Near-imperceptible: <=150ms, `opacity` or `background-color` only |
| Occasional (modal, drawer, toast, popover) | Standard motion from section 5 |
| Rare or first-time (onboarding, success, empty state, hero) | Delight allowed |

If the request fails the gate, say so and offer the non-motion alternative. Never animate data the user is reading or acting on for style (a live graph, a table being scanned).

## 2. Purpose: name it in one word

Feedback (the UI heard me), spatial consistency (where it came from or went), state indication (what changed), jarring-change prevention (content would teleport), explanation (how it works, marketing/onboarding only), delight (rare tier only). If you cannot name one, do not build it. "It looks cool" on something seen often is a reason to stop.

## 3. Tool ladder: cheapest that works

| Need | Tool |
| --- | --- |
| Hover, press, color, anything toggled by a class or attribute | CSS `transition` |
| Entering from `display:none` or on mount, no JS state | `@starting-style` + `transition-behavior: allow-discrete` |
| Predetermined, one-shot motion that must stay smooth while the page loads | CSS `@keyframes` (compositor) |
| Programmatic control, still compositor-grade, no dependency | WAAPI `element.animate()` |
| Springs, layout/shared-element animation, exit of unmounted components, gesture-driven values | A JS motion library |

Never add a library for a fade. Never use `@keyframes` for anything a user can trigger twice in a second or touch with a gesture: keyframes restart from zero, transitions and springs retarget from the live value.

If the task is really a component (drawer, toast, command menu, popover), prefer the project's accessible primitive for focus, Escape and dismissal, then add motion on top.

## 4. Properties

- Animate `transform` and `opacity`. They skip layout and paint. `clip-path` and `filter` are acceptable secondary tools; `height` only for accordions where no transform equivalent exists.
- Percent values in `translate()` are relative to the element's own size: `translateY(100%)` hides a sheet of any height. Prefer them over pixels for unknown-size elements.
- Never enter from `scale(0)`. Use `scale(0.9-0.97)` plus `opacity: 0`. Exception: an icon swapping in place (section 11) may start at `scale(0.25)` because it cross-fades with blur inside a fixed cell.
- `scale()` scales children too, which is why it reads as a physical press.
- Set `transform-origin` at the trigger for popovers, menus and tooltips. Modals are exempt and stay centered.
- Optional for content blocks entering: add `filter: blur(4px)` and `translateY(8-12px)`. Keep animated blur under 20px.
- Never `transition: all`. Name the properties.

## 5. Easing, duration, or a spring

Easing by situation:

| Situation | Curve |
| --- | --- |
| Enter or exit (default) | `--ease-out` |
| Moving or morphing while staying on screen | `--ease-in-out` |
| Hover, color, background | plain `ease` |
| Constant motion (marquee, progress, hold-to-confirm fill) | `linear` |
| Edge-attached sheet / drawer | `--ease-drawer` |
| Icon swap | `cubic-bezier(0.2, 0, 0, 1)` 300ms, or spring `{duration: 0.3, bounce: 0}` |

Never `ease-in` on UI: it delays the exact moment the user is watching. Built-in `ease-out` is too weak for deliberate motion; use the token. Do not hand-roll other curves.

Duration:

| Element | Duration |
| --- | --- |
| Press feedback | 100-160ms |
| Tooltip | 125-200ms |
| Dropdown, select, popover | 150-250ms |
| Modal, drawer, sheet | 200-500ms (500ms only for full-edge sheets; small floating drawers ~200ms) |
| Everything else in product UI | <=300ms |
| Marketing / explanatory | May run longer |

Exit is faster than enter (about enter 250ms, exit 150-200ms). A 180ms dropdown feels more responsive than a 400ms one, and a quick animation also reads as faster loading. Slow where the user is deciding (hold-to-confirm 2s linear), snappy where the system responds (release 200ms `--ease-out`).

Springs, for drag with momentum, interruptible gestures, decorative pointer tracking, and shared-layout motion:
- Default is no bounce: damping ratio 1 / `bounce: 0`. Add `bounce: 0.1-0.3` only after a momentum gesture (flick, throw). A button-closed sheet gets zero bounce because no force was applied.
- Smaller elements need slightly more bounce to read the same; large ones settle slowly and look heavier.
- Motion-style config: `{ type: "spring", duration: 0.5, bounce: 0.2 }`; physics form `{ mass: 1, stiffness: 100, damping: 10 }`. A spring that feels weird usually needs more damping.
- Apple-style reference values (damping ratio / response): reposition 1.0 / 0.4s, rotation 0.8 / 0.4s, sheet 0.8 / 0.3s.
- Pointer-tracking decoration (tilt, parallax) goes through a spring (`stiffness ~100, damping ~10`) instead of mapping raw coordinates; do it only on decoration, never on functional data.

## 6. Choreography: several things moving at once

Before writing timing, cast every moving element: Lead (what the user must notice, moves first and furthest), Support (follows, smaller motion), Decoration (fade only or nothing), Static (everything else). If two elements both want to be Lead, it is a hierarchy problem, not a timing problem. Most groups shrink here: a panel slides and its items are simply there.

Rules:
1. One entrance per container. If the panel animates in, its children do not also stagger.
2. Pick one relationship per pair: Together (same object or moment), Overlap/stagger (order matters, showing both is fine), Sequence (overlap would look broken, e.g. two dense views changing size in both axes: old leaves, then new enters), Carry (animate the parent and let children ride along, as with shared-layout items landing in a bin).
3. Stagger: list/collection items 30-60ms apart with the whole sequence <=300ms (stagger only the first handful; later items share the last delay). Semantic content chunks on marketing/hero surfaces 80-100ms. Make delays and distances non-uniform (lead travels most, gaps shrink); uniform gaps read as a metronome.
4. Never stagger high-frequency or keyboard-triggered UI (menus, command palettes, tab content, rows reopened daily). Stagger is decorative and must never block interaction: everything visible is clickable.
5. An element that leaves stops occupying space at once, so siblings close the gap while it fades (remove from flow or use the library's pop-layout mode). Otherwise the gap closes only after the exit and the whole thing feels slow.
6. View swaps in one container are a crossfade: old exits while new enters. Sub-animations of one component share one duration and easing family; a 500ms open plus a 200ms resize reads as two objects. Derive resize-fade duration from the size delta and clamp (about `clamp(150ms, deltaHeight/500, 270ms)`).
7. Direction follows the mental model: forward enters from the right, back from the left. Pass direction to exiting children explicitly, because exits read state when they start.
8. Secondary motion hooks to the moment it belongs to (the click, not the hover). A repeat action skips the buildup.
9. Idle attention loops wait ~2.5s before the first run, repeat rarely (~6s), stop during interaction and resume only after the reset finishes.

## 7. Interruption and exit

- Interactive state uses transitions, not keyframes. Clicking again mid-flight reverses from the current value.
- Gestures and anything draggable use springs, which carry velocity through a retarget. Always animate from the live on-screen value, never the logical target, or it visibly jumps.
- Never lock input during a transition. A closing sheet the user grabs must follow the finger.
- Re-trigger mid-sequence stops in-flight animations before starting new ones. Never stack them. Chain only after all resets finish (`Promise.all`).
- Exit mirrors entry path: it entered from the bottom, it leaves through the bottom (this also makes swipe-to-dismiss discoverable). Exits are simultaneous (no reverse stagger), shorter and subtler: opacity plus a small move (`-12px`, optional `blur(4px)`), not the full container height unless the destination carries meaning (a drawer closing, a card returning to its list).
- Skip entrance on first render for state swaps (default icon, selected tab), but never for an intentional hero entrance. Check with a full page refresh.
- Rapid switching can leave two children mounted: give each view a stable unique key and test it.

## 8. Gestures: track, then release

```
pointer down?
  yes -> TRACK: position = pointer, 1:1. No easing, duration or spring.
  no  -> RELEASE: physics finishes it (spring seeded with release velocity,
         or a CSS transition for CSS-only components). Never a keyframe.
```

Track phase:
- Pointer Events with `setPointerCapture`; keep the grab offset (do not snap the element's center to the pointer). Ignore extra touch points while dragging.
- Require ~10px hysteresis before committing to an axis. Run candidate gestures in parallel and cancel losers once intent is clear; avoid recognizers that only report a final state.
- Write `transform` on the dragged element directly. Never push drag distance through a CSS variable on a parent (every descendant restyles, and drags get laggy past ~20 children). No framework state per `pointermove`; use refs or motion values.
- Set `touch-action` on the drag axis (`pan-x` for a vertical drag, etc.) so native scroll does not fight.
- Respond on pointer-down, not release. Feedback is continuous during the interaction.
- Linked values (overlay opacity, page scale behind a sheet) derive from one drag-progress value, never a separate animation, so they stay in sync through reversals.

Release phase:
- Dismiss on distance OR velocity: pass ~40-50% of travel, or flick fast. Velocity is `abs(distance)/elapsedMs`; above about `0.11` px/ms dismiss regardless of distance.
- Project where the gesture is going, then snap to the nearest target to that point instead of to the release point:
  ```js
  const project = (v /* px/s */, rate = 0.998) => (v / 1000) * rate / (1 - rate);
  const end = position + project(releaseVelocity);
  ```
  (`0.99` for a snappier feel.) Hand the release velocity to the spring. If the API wants relative velocity: `v / (target - current)`.
- Rubberband at boundaries: resistance grows with overshoot, never a hard stop.
  ```js
  const rubberband = (over, dim, c = 0.55) => (over * dim * c) / (dim + c * Math.abs(over));
  ```
- Snap-back when the threshold is missed is fast. A slow return feels like the UI disagreeing with the user.
- Decompose 2D into independent X and Y springs. Blend velocity through a reversal.
- Enter and exit on the same axis, so the dismiss direction is felt rather than learned.
- Bottom sheets and drawers are weeks of edge cases (focus trap, Escape, momentum). Prefer an existing sheet primitive; hand-roll only for bespoke gestures. Mark interactive children as non-draggable.
- Hold gestures are asymmetric: `linear` while holding (it visualizes time), fast `--ease-out` cancel on early release.
- Test on a real phone through the dev server's IP; trackpad emulation and a 60Hz monitor hide roughness.

## 9. Performance

Budget: 16.7ms per frame at 60Hz, 8ms at 120Hz. Design for the smaller. The developer's laptop is the least representative device.

| Tier | Properties | Cost |
| --- | --- | --- |
| Composite only | `transform`, `opacity` (`filter`, `clip-path`, `background-color` increasingly) | Cheapest |
| Paint + composite | `box-shadow`, `border-radius`, `color` | Redraw per frame |
| Layout + paint + composite | `width`, `height`, `padding`, `margin`, `top`, `left`, `border-width` | Most expensive, moves neighbors |

Swap down a tier:

| Instead of | Use |
| --- | --- |
| `width` / `height` / `padding` growth | `scale()` |
| `top` / `left` / `margin` movement | `translate()` |
| `display` / `visibility` toggling | `opacity`, then `visibility: hidden` at the end so it stops taking clicks |
| animated `box-shadow` | `filter: drop-shadow()` (measure outside Chromium), or fade a pseudo-element's `opacity` |

Where it runs: JS `requestAnimationFrame` loops and library layout animations stay on the main thread and drop frames when the page loads, hydrates or navigates. CSS transitions/keyframes and WAAPI (`opacity`, `transform`, usually `filter`, `clip-path`) can run off-thread. So: CSS for predetermined motion and anything that plays while the page is busy (a tab indicator during navigation); a JS library for dynamic, interruptible, gesture-driven motion. Library shorthand props (`x`, `y`, `scale`) are only compositor-accelerated in recent versions; animating a full `transform` string is accelerated everywhere. Layout animations, per-frame motion values and inertia always stay on the main thread.

Further traps:
- Per-frame `setState` in `rAF`, `pointermove` or scroll handlers: write `el.style.transform` or a motion value instead.
- Animated inherited custom properties (`--swipe-amount` on a parent): set `transform` on the element.
- Animated `blur()` over 20px, especially in Safari; 2-5px is enough to soften a crossfade.
- Long lists: virtualize.

`will-change` is a last resort for a visible 1px shift at animation start/end or measured dropped frames. Only `transform`, `opacity`, `filter`; name the property; put it on the one animating element, never `*` or large containers, and never on something that does not animate. A promoted ancestor becomes the containing block for `position: fixed` descendants and creates a stacking context.

Diagnose in order: which properties animate; does it coincide with a busy main thread; React re-rendering per frame; does it worsen as content grows; inherited variable; blur size; then `will-change`. Confirm with a recording (Performance panel, Animations panel at slow speed) on a mid-range phone, not by feel.

## 10. Reduced motion and hover gating

Reduced motion means gentler, not zero. Replace slides, springs, parallax, scale and blur with a short opacity cross-fade; keep color and opacity changes that aid comprehension; drop bounce and momentum flings. Direct manipulation (tracking a drag) stays because the user produces it. A sequence that is itself the explanation jumps between frames. Also avoid full-viewport moving backgrounds and abrupt brightness jumps. See `accessibility` for the full policy.

```css
.panel { transition: opacity 150ms var(--ease-out); }
@media (prefers-reduced-motion: no-preference) {
  .panel {
    transition-property: opacity, translate, scale, filter;
    transition-duration: 250ms;
  }
}
@media (hover: hover) and (pointer: fine) {
  .card:hover { translate: 0 -2px; }
}
```

For translucent materials also honor `prefers-reduced-transparency` (see `interface-polish`).

## 11. Recipes (framework-neutral CSS first)

**Button press** (any pressable): `scale(0.97)`, allowed 0.95-0.98. Offer an opt-out (variant or `static` prop). No hover gate needed on `:active`. Under reduced motion keep a color/opacity press state and gate the scale (section 10).
```css
.btn { transition: transform 160ms var(--ease-out); }
.btn:not(:disabled):active { transform: scale(0.97); }
.btn[data-static]:active { transform: none; }
```

**Popover / dropdown / menu / select**: scale from the trigger, 150-250ms.
```css
.popover {
  transform-origin: var(--transform-origin, top center); /* set from the trigger rect */
  transition: opacity 200ms var(--ease-out), transform 200ms var(--ease-out);
  @starting-style { opacity: 0; transform: scale(0.95); }  /* enter */
}
/* exit: a class or data-state your code sets before unmounting */
.popover[data-state="closed"] { opacity: 0; transform: scale(0.95); transition-duration: 150ms; }
```
If the element toggles `display` (native popover/dialog), add `transition-behavior: allow-discrete` (see "Enter from display:none" below).

**Tooltip**: same shape, 125ms, `scale(0.97)`. Delay before the first open to avoid accidental activation; once one is open, neighbors open with no delay and `transition-duration: 0ms`.

**Modal**: centered, 250ms `--ease-out`, `scale(0.96)` + opacity; fade the backdrop's opacity at the same duration so they read as one surface.

**Drawer / sheet**: `transform: translateY(100%)` closed, `translateY(0)` open, `500ms var(--ease-drawer)` for edge-attached; ~200ms for small floating ones. Drag handling per section 8.

**Toast**: transitions, not keyframes, because toasts arrive rapidly. Enter 300ms `--ease-out`, exit faster. Enter from `translateY(100%)` and leave the same way; swipe along that axis dismisses.
```css
.toast {
  opacity: 1; transform: translateY(0);
  transition: opacity 300ms var(--ease-out), transform 300ms var(--ease-out);
  @starting-style { opacity: 0; transform: translateY(100%); }
}
```
Fallback without `@starting-style`: set a `data-mounted` attribute after first paint. When toasts stack, tune opacity against height by eye. Pause timers while the tab is hidden; fill gaps between stacked toasts so hover is not lost.

**Accordion / collapse**: `height` + `opacity`, 200ms `--ease-out`, `overflow: hidden`. One of the few tolerated layout animations, so keep it short. Measure content height (or use a primitive that supplies it); never animate to `auto`.

**Tabs indicator**: for a sliding underline or pill, transition `translate` + `width` (or `scale`) with `--ease-in-out` ~250ms. For perfect color-in-sync, duplicate the tab row styled as active, clip the copy to the active tab with `clip-path: inset(0 60% 0 20%)` and animate the clip. Keep indicator motion in CSS if it runs during navigation.

**Icon swap** (play/pause, copy/copied; infrequent only): keep both icons in one grid cell and cross-fade. Scale 0.25 to 1, opacity 0 to 1, blur 4px to 0, 300ms `cubic-bezier(0.2,0,0,1)`, bounce always 0. Under reduced motion only the opacity runs. Do not animate tab-icon swaps or hover-revealed row actions; those are high-frequency.
```css
.icon-stack { display: grid; place-items: center; }
.icon-stack > * {
  grid-area: 1 / 1;
  transition: opacity 300ms cubic-bezier(0.2,0,0,1), scale 300ms cubic-bezier(0.2,0,0,1), filter 300ms cubic-bezier(0.2,0,0,1);
}
.icon-stack > [data-hidden] { opacity: 0; scale: 0.25; filter: blur(4px); }
```

**List / hero stagger** (occasional only): per-item `--delay`, `animation-fill-mode: both` so delayed items never flash their end state, hidden state lives in the `from` keyframe.
```css
.stagger > * { animation: enter 300ms var(--ease-out) both; animation-delay: var(--delay, 0ms); }
@keyframes enter { from { opacity: 0; transform: translateY(8px); } }
@media (prefers-reduced-motion: reduce) { .stagger > * { animation-name: fade; animation-delay: 0ms; } }
@keyframes fade { from { opacity: 0; } }
```
Set `--delay` per item (0, 40, 70, 90ms...), shrinking gaps, cap at ~300ms total.

**Hold to confirm** (destructive actions): overlay `clip-path: inset(0 100% 0 0)`; on `:active` go to `inset(0)` over 2s `linear`; release returns over 200ms `--ease-out`; add the press scale. Also provide a keyboard path that does not rely on holding (see `accessibility`).

**Enter from display:none** (native popover/dialog): `@starting-style` plus `transition-behavior: allow-discrete`, with `display` (and `overlay` where supported) in `transition-property`. Unsupported browsers show and hide instantly.

**Crossfade that will not settle**: add `filter: blur(2px)` and `opacity: 0.7` during the swap; blur blends two overlapping objects into one perceived change.

**Scroll reveal** (marketing only): `clip-path: inset(0 0 100% 0)` to `inset(0)` on first intersection, once. Never on functional UI visited daily.

**WAAPI without a library**:
```js
el.animate([{ clipPath: 'inset(0 0 100% 0)' }, { clipPath: 'inset(0)' }],
  { duration: 600, fill: 'forwards', easing: 'cubic-bezier(0.77,0,0.175,1)' });
```

**Theme switch**: suppress all transitions for the swap, or every color animates at once and smears. Recipe in `interface-polish`.

Library notes (only if the project already uses one): pass `initial={false}` on state swaps, a stable `key` on exiting children, `custom` direction on both the presence wrapper and the child, pop-layout mode for exits that free space, and one shared transition config for the whole component.

## 12. Never ship / before you finish

| Detect | Fix |
| --- | --- |
| `transition: all` / `transition-all` | Name the exact properties |
| `scale(0)` entrance | `scale(0.9-0.97)` + `opacity: 0` |
| `ease-in` on UI | `--ease-out` (or `--ease-in-out` for on-screen moves) |
| Built-in `ease-out` on a deliberate entrance | `--ease-out` token |
| Any motion on a keyboard action or 100+/day surface | Remove; instant state change |
| UI duration over 300ms with no reason (not modal/drawer/sheet) | 150-250ms |
| Exit as slow as enter | Exit 150-200ms vs enter ~250ms |
| `transform-origin: center` on a trigger-anchored popover | Origin at the trigger (modal stays centered) |
| `@keyframes` on toggles, toasts or anything gesture-touched | Transition or spring |
| Animating `width`, `height`, `top`, `left`, `margin`, `padding` | `transform` / `opacity` (accordion height excepted) |
| Press scale outside 0.95-0.98, no `:disabled` guard, or no opt-out | `0.97`, `:not(:disabled)`, opt-out |
| Bounce above 0 on a button-triggered or non-momentum motion | Bounce 0 |
| Ungated `:hover` motion | `@media (hover: hover) and (pointer: fine)` |
| Missing `prefers-reduced-motion` handling | Opacity fallback, not zero |
| Parent and children both animate entrance | One entrance per container |
| Uniform or long stagger; stagger on menus or key-driven UI | 30-60ms, non-uniform, <=300ms total; none on high-frequency |
| Drag distance via CSS variable on parent, or state per move | Direct `transform` write, refs/motion values |
| Distance-only dismiss; hard stop at edge; slow snap-back | Velocity or distance; rubberband; fast spring |
| Exit leaves a layout gap until it ends | Take it out of flow at removal time |
| `will-change` on `*`, `all`, or non-animating elements | Remove, or name the animated property, last resort only |
| Motion is the only signal of a state change | Add a static cue |
| Feel can't be judged from code | Replay at 2-5x duration or 10% speed in the Animations panel, step frame by frame, test gestures on a device, look again the next day |

## Credits

Synthesized for Quark from Emil Kowalski's skills (github.com/emilkowalski/skills) and Jakub Krehel's skills (github.com/jakubkrehel/skills), both MIT.
