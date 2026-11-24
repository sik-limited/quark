---
name: layout
description: Set grouping, spacing, alignment, reading order, logical properties, responsive structure and mobile viewport behavior so a layout survives resizing, translation and RTL. Use when building or reviewing page or component layout, spacing, responsive behavior, RTL support, full-height mobile screens, sticky chrome or safe areas.
license: MIT
---

# Layout

Use this when arranging components on a page or building a component's internal layout. It sets grouping, alignment and structure, then stress-tests them by resizing, translating and mirroring. Write fixes in the project's styling system and tokens (`--q-space`, `--q-gap`, `--q-panel-padding`); the numbers below are starting points only where no spacing scale exists. A finding needs a failure you can show (clip, overlap, broken mirror, misread group), not a value that merely differs from these defaults.

Hit areas and focus belong to `accessibility`; radius, shadows to `interface-polish`; line length and truncation mechanics to `typography`; wording to `interface-copy`.

## 1. Grouping and spacing

Group with space first, background shapes second, separator lines last.

1. Negative space is the default.
2. A background shape (card, filled container) where a group must read as one unit: a selectable row, a draggable card.
3. Separator lines only for dense data where space costs too much (tables, long settings lists). Keep them low-contrast hairlines; never pair a line with a large gap that already did the job.

Ratio: the gap between groups is at least 2x the gap within one (8px inside, 16px+ between).

```css
.field-group { display: flex; flex-direction: column; gap: 8px; }
.form        { display: flex; flex-direction: column; gap: 24px; }
```

Bad: uniform spacing everywhere plus borders to compensate.

## 2. Controls vs content

Every interactive element has a shape, border, underline or consistent placement zone (toolbar). A control styled like adjacent static text does not read as a control. The inverse also holds: a non-clickable badge shaped like the buttons beside it collects dead clicks.

## 3. Alignment

- Pick a small set of alignment edges and put everything on them. Typical stray edges: an icon 2px off the text edge, a card padded unlike its neighbor.
- One spacing step per nesting level; deeper nesting repeats the step, it does not invent new ones.
- Numbers in tables `text-align: end`, text `start` (tabular figures: `typography`).

```css
.section { padding-inline: 24px; }
.section .child { margin-inline-start: 16px; }
```

## 4. Logical properties and RTL

Anything direction-dependent is written as start/end.

| Physical | Logical |
| --- | --- |
| `margin-left` / `padding-right` | `margin-inline-start` / `padding-inline-end` |
| `left: 0` | `inset-inline-start: 0` |
| `text-align: left` | `text-align: start` |
| `border-right` | `border-inline-end` |
| `float: left` | `float: inline-start` |
| `border-top-left-radius` | `border-start-start-radius` |
| `width` / `height` (when flow-relative) | `inline-size` / `block-size` |

- Flex rows and grid columns mirror under `dir="rtl"` on their own. Physical values, `translateX`, `background-position` and hand-positioned `left/right` do not; they need an explicit `[dir="rtl"]` override (they have no logical form).
- Progression encodes direction: star ratings, step indicators and progress bars mirror (fill from the leading edge). Digit order inside numbers never reverses.
- Directional icons (arrows, chevrons, back) flip; logos, media controls and clocks do not (full list: `interface-polish`).
- Safe-area insets are physical. Pair each physical side with its own inset and override in RTL; do not mix `inset-inline-*` with `safe-area-inset-left/right`.

```css
.fab {
  position: fixed;
  right: calc(16px + env(safe-area-inset-right));
  bottom: calc(16px + env(safe-area-inset-bottom));
}
[dir="rtl"] .fab { right: auto; left: calc(16px + env(safe-area-inset-left)); }
```

## 5. Order and primary action

- Most important content near the top and the leading edge. In a row: identifying content leads, metadata and actions trail. Do not bury the one number the user came for under secondary detail; collapse the rest into sections, tabs or detail views.
- DOM order equals visual order. Never reorder with `order`, `row-reverse` or grid placement; focus order and screen readers follow the DOM.
- One primary action per view. More than 3 secondary actions go behind a menu. Prefer a short view that links deeper over one long view showing everything. (Color of the primary: `design-tokens`.)

## 6. Hidden content needs a cue

Every off-screen or collapsed item has a visible cue.

- Scrollers: let the next item peek 16-32px past the edge. A row ending exactly at the container edge looks complete and nobody scrolls it.
- Collapsed sections: chevron or a control naming what is hidden ("Show 12 more results", not "More").
- Truncated values the user needs: expand control, detail link, or a tooltip that also opens on keyboard focus. A hover-only `title` does not count.

Peek width: item width = `100%` + gap - peek (100% is the content box).

```css
.scroller {
  display: flex; gap: 12px; overflow-x: auto;
  padding-inline: 24px; scroll-padding-inline: 24px;
  scroll-snap-type: x mandatory;
}
.scroller > * { flex: 0 0 calc(100% - 12px); scroll-snap-align: start; } /* 24px peek */
```

## 7. Clearance

Where no density system exists:

| Between | Start with |
| --- | --- |
| Adjacent bordered or filled controls | 12px |
| Visible glyphs of borderless text/icon controls (counting each button's padding) | 24px |
| Unrelated control groups | 24px+ |

Nothing marks where one borderless target ends, so space is the boundary. Keep an established usable density rather than inflating controls; just never let hit areas overlap.

Inset action buttons from edges: 16px inline margin on mobile, `padding-bottom: calc(16px + env(safe-area-inset-bottom))`. Edge-to-edge only when it follows platform chrome, respects safe areas and stays distinct from system UI. Never `width: 100vw` (includes the scrollbar).

## 8. Content bleeds, controls float

Backgrounds and media extend to viewport edges; controls and text stay inside margins and safe areas.

```css
.article { display: grid; grid-template-columns: 1fr min(65ch, calc(100% - 48px)) 1fr; }
.article > * { grid-column: 2; }
.article > .full-bleed { grid-column: 1 / -1; }
```

## 9. Responsive structure

- Breakpoints come from content, not device presets: keep the expanded layout until it stops fitting (sidebar squeezes content under its minimum measure, card grid below a usable column width), then collapse.
- Components adapt to their container, not the viewport. A viewport `@media` inside a reusable component breaks it in a narrow sidebar.

```css
.card-list { container-type: inline-size; }
@container (width < 400px) { .card { grid-template-columns: 1fr; } }
```

- A query styles descendants only, never the container itself. `container-type: inline-size` applies size containment: a shrink-to-fit box (flex item, inline-block, absolute element with no width) collapses to zero, so give it a definite width.
- Test the smallest and largest supported sizes first, plus 320px and 200% zoom (`accessibility`). Use `rem`/`em` in media queries.

## 10. Growth rules

Translated strings grow, and short ones grow proportionally more; a one-word button label is the riskiest text on screen.

- Size text containers with `max-width` and `min-height`, never fixed width or height. Let rows wrap (`flex-wrap: wrap; gap`).
- A grid track or flex child will not shrink below its content's minimum. Use `grid-template-columns: minmax(0, 1fr)` and `min-width: 0` on flex children so long words and wide tables do not blow out the layout.
- A box that must cap height: `max-height` plus `overflow-y: auto`.
- Never park a critical action where resize, zoom or scroll can clip it. Keep it in normal flow or stable chrome.
- Modal: `max-height: 100dvh`, scrolling body, action row outside the scroll area.

```css
.button { padding-inline: 16px; max-width: 100%; }       /* label defines the size */
.row { display: flex; flex-wrap: wrap; gap: 12px; }
```

Test with pseudo-localization (wrap strings as `[!!! Ĥéļļö ŵöŕļð ~~~~ !!!]`, expand ~40%, add accents) and one long-string locale such as German. Also test an RTL mirror.

## 11. Mobile viewport

- `100vh` is the largest viewport (URL bar collapsed) and overflows on load. App shells and drawers: `height: 100dvh`. Heroes and first screens: `min-height: 100svh` (stable, never cut off). Avoid `lvh`. Keep a `100vh` fallback line only if the support matrix demands it. Never `100vw` for width: use `100%`.
- The on-screen keyboard resizes only the visual viewport; keep the bottom action in stable chrome (sticky footer with safe-area padding or top of view).
- Notch and home indicator: `viewport-fit=cover` in the viewport meta, then pad with `env(safe-area-inset-*, 0px)`. Without `viewport-fit=cover` every inset is `0`. Fixed headers, tab bars, toasts and sheets need this.
- Overscroll: `overscroll-behavior: none` on `html, body` for app-style shells (kills pull-to-refresh and rubber-band); `contain` on inner scrollers (sheets, chat lists, sidebars) to stop chaining while keeping their own bounce. Never `touchmove` + `preventDefault`.
- Sticky header: `html { scroll-padding-block-start: var(--header-height) }` so anchors and focused elements never land under it; sticky footers get `scroll-padding-block-end`.
- Inputs 16px+ so iOS does not zoom; never `maximum-scale=1`. Details: `typography`, `accessibility`.
- Test layouts on real hardware; emulation does not reproduce URL-bar height, safe areas or the keyboard.

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
```

```css
html, body { overscroll-behavior: none; }
.app { height: 100dvh; }
.hero { min-height: 100svh; }
.sheet-content { overflow-y: auto; overscroll-behavior: contain; padding-bottom: calc(1rem + env(safe-area-inset-bottom, 0px)); }
```

## Before you finish

| Pattern | Fix |
| --- | --- |
| `grid-template-columns: ... 1fr` or flex child holding long text/wide table overflowing | `minmax(0, 1fr)` on track, `min-width: 0` on child |
| `width: 100vw` | `width: 100%` |
| `height: 100vh` on a full-height mobile pane | `100dvh` (app) or `min-height: 100svh` (hero) |
| Fixed `height` on a box holding text | `min-height`, or `max-height` + `overflow-y: auto` |
| `@media (max-width)` inside a reusable component | `@container` on the parent |
| `container-type: inline-size` on a width-less flex item/inline-block/absolute box | Give it a definite width |
| `env(safe-area-inset-*)` with no `viewport-fit=cover` | Add the meta value |
| `inset-inline-*` mixed with `safe-area-inset-left/right` | Physical side + inset, `[dir="rtl"]` override |
| `margin-left`, `padding-right`, `text-align: left` on mirrored UI | Logical equivalent |
| `translateX` / `background-position: left` on mirrored UI | `[dir="rtl"]` override |
| `order`, `row-reverse` changing meaning | Reorder the DOM |
| Sticky header with no `scroll-padding-block-start` | Set it to the header height |
| Scroller ending exactly at its edge | Peek 16-32px |
| Truncated value with no route to full text | Expand, detail link or keyboard-reachable tooltip |
| Several borderless icon buttons packed at 4px | 24px between glyphs |
| Border plus big gap for the same grouping | Keep the gap, drop the line |

Severity: HIGH when content or an action is blocked at a supported viewport (clip/overlap at 320px or 200% zoom, truncation with no way to the full value, hidden content with no cue). MEDIUM harms hierarchy, reading order or adaptability. LOW is isolated alignment or spacing polish. Report checks you could not run as not verified.

## Credits

Synthesized for Quark from Emil Kowalski's skills (github.com/emilkowalski/skills) and Jakub Krehel's skills (github.com/jakubkrehel/skills), both MIT.
