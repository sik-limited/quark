---
name: interface-polish
description: Polish surfaces and small visual details with exact values for nested border radius, optical alignment, shadow-as-border, image outlines, icon stroke/size/RTL, translucent materials, theme-switch behavior and other invisible details. Use when building or reviewing cards, buttons, panels, images, icons, toolbars, sheets, or when a UI looks almost right but "off".
license: MIT
---

# Interface Polish

Use this when the structure and interaction already work and the remaining problem is how surfaces, edges, icons and materials look. A polish finding never outranks a broken interaction, accessibility failure or layout bug.

Ground rules:
- Values are exact, except optical nudges and the concentric-padding cutoff, which are starting points judged by eye.
- Keep the project's tokens, density and component library. A deliberate, consistent convention (for example a flat style with no shadows) is a preference, not a defect. The same detail done two ways inside one project is a defect.
- Express values through the project's tokens. Quark components read `--q-radius`, `--q-border`, `--q-border-width`, `--q-shadow`, `--q-surface`, `--q-panel-padding`; extend them rather than hardcoding repeats.
- Movement, easing, press scale, stagger and exits belong to `animate`. Text wrapping, numerals and tracking belong to `typography`. Contrast and ramps belong to `design-tokens`. Hit targets, focus and ARIA belong to `accessibility`. Spacing, grouping and RTL layout belong to `layout`.

## 1. Concentric radius

Where nested surfaces share a visible, even inset:

```
outerRadius = innerRadius + padding + borderWidth
```

```css
.card  { border-radius: calc(var(--inner-radius) + var(--card-padding)); padding: var(--card-padding); }
.card > .media { border-radius: var(--inner-radius); }  /* e.g. 12 inner + 8 padding = 20 outer */
```

- Same radius on a padded parent and its child is the classic defect: the gap looks pinched at the corners.
- Past about `24px` of padding, or where padding is deliberately asymmetric, treat the layers as separate surfaces and keep each one's own radius token.
- Inner radius never goes negative: if padding exceeds the outer radius, the inner radius is `0`.

## 2. Optical alignment

Geometric centering often looks wrong. Nudge by eye, then stop.

- Button with text and an icon: give the icon side about `2px` less padding than the text side (`padding-inline-start: 16px; padding-inline-end: 14px` for a trailing icon). Use logical properties so RTL flips correctly.
- Play triangle: shift about `2px` toward its point (physical `translateX(2px)` on purpose; playback icons never mirror).
- Asymmetric glyphs (stars, arrows, carets): fix the `viewBox` or path in the SVG so no component needs an offset. Only nudge a wrapper when the asset cannot change.
- Icon beside text: align the icon to the cap height or x-height center, not the line box, when the two look uneven.
- Avatars and fixed-size media in flex rows need `flex-shrink: 0` or they squash into ovals.

## 3. Shadow as border, border as structure

Where a border exists only to create depth or separate a card from its background, replace it with layered transparent shadows. They blend with any background and are not a hard line.

```css
:root {
  --shadow-border:
    0 0 0 1px oklch(0 0 0 / 0.06),
    0 1px 2px -1px oklch(0 0 0 / 0.06),
    0 2px 4px 0 oklch(0 0 0 / 0.04);
  --shadow-border-hover:
    0 0 0 1px oklch(0 0 0 / 0.08),
    0 1px 2px -1px oklch(0 0 0 / 0.08),
    0 2px 4px 0 oklch(0 0 0 / 0.06);
}
/* redefine under the project's dark selector: depth shadows vanish on dark, keep one ring */
:root[data-theme="dark"] {
  --shadow-border: 0 0 0 1px oklch(1 0 0 / 0.08);
  --shadow-border-hover: 0 0 0 1px oklch(1 0 0 / 0.13);
}

.card {
  border: 1px solid transparent;  /* forced-colors mode drops box-shadow and paints this */
  box-shadow: var(--shadow-border);
  transition: box-shadow 150ms ease;
}
@media (hover: hover) and (pointer: fine) { .card:hover { box-shadow: var(--shadow-border-hover); } }
```

- Keep real borders on dividers, separators, table cells, selected states and form inputs (an input boundary needs 3:1 non-text contrast; see `accessibility`). Focus rings are owned by `accessibility`.
- The transparent border keeps the box size identical to a bordered one.
- Larger surfaces read as thicker: stronger shadow and, for glass, stronger blur than small chips. Heavier shadow over busy content, lighter over plain backgrounds.
- Animating shadows is expensive; transition them only on hover-scale surfaces and keep it 150ms (see `animate` for cheaper alternatives).

## 4. Image outlines

Content images (photos, avatars, thumbnails) get a `1px` outline inset by `1px`, so edges stay crisp against any background.

```css
:root { --image-outline: oklch(0 0 0 / 0.1); }
:root[data-theme="dark"] { --image-outline: oklch(1 0 0 / 0.1); }
.content-image { outline: 1px solid var(--image-outline); outline-offset: -1px; }
```

- Pure black in light, pure white in dark. Never a palette near-black, a tinted neutral or the accent color: the tint shows as a colored fringe.
- Use `outline`, not `border`: it never changes the image size and follows the corner radius.
- Skip transparent artwork such as logos and illustrations.

## 5. Icons

**Stroke tracks text weight** (rendered pixels, not SVG units):

| Adjacent text | Stroke |
| --- | --- |
| Regular 400, 14-16px | `1.5px` |
| Medium / semibold 500-600 | `2px` |
| Bold 700, or emphasized standalone icon | `2.5px` |

A 24px-grid icon drawn at 16px scales its stroke by 16/24, so `stroke-width: 2` paints about `1.33px`. Use the library's absolute-stroke option or `vector-effect: non-scaling-stroke`.

- One icon library per surface. Never mix libraries in one toolbar.
- Size inline icons `1em`-`1.25em` of the adjacent font size so the pair scales together.
- Design at render size: check recognizability at the smallest size (often `16px`). Use the per-size grid (16/20/24) matching the render size; a 24px drawing scaled to 16px lands off the pixel grid and looks soft. Always SVG, never raster.
- **One SVG, recolored per state.** Use `currentColor` for fill/stroke and let CSS color and opacity drive hover, selected and disabled. No hardcoded `fill="#..."` and no separate assets per state.
  ```css
  .icon-btn { color: var(--q-muted); }
  @media (hover: hover) and (pointer: fine) { .icon-btn:hover { color: var(--q-text); } }
  .icon-btn[aria-pressed="true"] { color: var(--q-accent); }
  .icon-btn:disabled { opacity: 0.4; }
  ```
- **Outline default, fill active.** Outline for default state in toolbars, rows and inline text; fill marks the selected/active state (active tab, toggled bookmark, liked heart). Never use the pair interchangeably. Cross-fade the swap only for infrequent changes (recipe in `animate`); an active tab swaps instantly.
- **RTL.** Under `dir="rtl"` flip only icons whose meaning follows reading direction:

  | Flip | Do not flip |
  | --- | --- |
  | Back/forward arrows, navigation chevrons | Logos and brand marks |
  | Alignment, list and indent glyphs | Checkmarks |
  | Speaker/volume waves | Physical objects (clocks, cups, pencils) |
  | Directional glyphs such as "send" | Media playback controls |

  Use one mechanism per element (`[dir="rtl"] .icon-directional { scale: -1 1; }`); two flips compound back to unflipped. For composite icons (badge, slash overlay) flip the base glyph and keep the overlay's position. Accessible names for icon-only buttons belong to `accessibility`.

## 6. Materials and depth

Translucent layers (nav bars, toolbars, sheets, popovers) convey hierarchy without opaque strips.

```css
.toolbar {
  background: color-mix(in oklab, var(--q-surface) 70%, transparent);
  backdrop-filter: blur(20px) saturate(180%);
  border-top: 1px solid oklch(1 0 0 / 0.4);   /* light edge reads as light catching the material; use a dark-mode equivalent */
}
@media (prefers-reduced-transparency: reduce) {
  .toolbar { background: var(--q-surface); backdrop-filter: none; }
}
@media (prefers-contrast: more) {
  .toolbar { background: var(--q-surface); border: var(--q-border-width) solid var(--q-border); }
}
```

- Build floating chrome as translucent layers with content scrolling underneath, rather than fixed opaque strips.
- Material weight encodes hierarchy: heavier/darker for structural regions (sidebars), lighter for interactive emphasis.
- Never stack a light translucent surface on another light one: legibility collapses. Put color on a solid layer, not on the translucent foreground.
- Over blurred surfaces avoid flat gray text. Raise contrast, a touch more weight and a small tracking bump. Verify contrast against the worst backdrop (see `design-tokens`).
- Scroll edge effects over hard dividers: where content slides under floating chrome, fade with a small blur or gradient mask instead of a 1px rule, and only where the overlap actually exists.
- Dim to focus, separate to keep flow: a modal task uses a scrim and pushes the background back; a parallel non-blocking panel uses translucency and offset with no scrim. Stacked sheets progressively dim each parent.
- Blur is expensive, especially in Safari and on large areas; keep it on small or fixed regions. Animated blur (materialize on enter) stays under 20px.
- `backdrop-filter` needs the element or its ancestors to not clip it, and creates a stacking context; check z-order after adding it.

## 7. Theme switch

When theme changes, every color transition fires at once and the switch smears. Suppress transitions for the swap, force a style flush, restore after the next frame:

```js
function withoutTransitions(change) {
  const style = document.createElement('style');
  style.textContent = '*,*::before,*::after{transition:none !important}';
  document.head.append(style);
  change();                       // flip the theme attribute / class
  void document.body.offsetHeight; // synchronous style flush
  requestAnimationFrame(() => requestAnimationFrame(() => style.remove()));
}
// also call it from a matchMedia('(prefers-color-scheme: dark)') change listener
```

Ease abrupt dark/light jumps for users sensitive to brightness changes where possible, but never with per-element transitions.

## 8. Small details cheatsheet

| Symptom | Fix |
| --- | --- |
| Numbers jitter when they change | `font-variant-numeric: tabular-nums` (see `typography`) |
| Large headings look loose | Tighter tracking, `-0.01em` to `-0.02em` at 24px and up (see `typography`) |
| Body line too long | `max-width: 60ch`-`75ch` (see `typography`) |
| Gradient fade has a visible edge or band | Eased gradient: add extra color stops along an ease curve instead of two linear stops |
| Button feels dead | Press feedback per `animate` (`scale(0.97)` on `:active`) |
| Tap flashes a gray box on mobile | `-webkit-tap-highlight-color: transparent` where the control draws its own pressed state |
| Hover state sticks after a tap on touch | Wrap hover styles in `@media (hover: hover) and (pointer: fine)` |
| Scrolling a dialog, drawer or menu scrolls the page behind | `overscroll-behavior: contain` on the scrollable overlay |
| Tiny control, hit area too small to grow visually | Invisible `::before` hit area; 24px minimum, 44px recommended on touch (see `accessibility`) |
| Hover lost between stacked or adjacent items | Bridge the gap with a pseudo-element so the pointer stays on the group |
| Old timestamps read "340 days ago" | Switch to an absolute date after about a week |
| Count reads "1 members" | Pluralize with `Intl.PluralRules`, never string concatenation |
| Transition between two states looks off | Brief `filter: blur(2px)` during the swap (see `animate`) |
| Avatar squished into an oval | `flex-shrink: 0` and fixed `aspect-ratio` |

## Before you finish

| Detect | Fix |
| --- | --- |
| Padded parent and child with the same `border-radius` | Outer = inner + padding (+ border) |
| `box-shadow: 0 0 0 1px` ring with no border beside it | Add `border: 1px solid transparent` |
| Image outline in a palette gray, hex or accent color | `oklch(0 0 0 / 0.1)` light, `oklch(1 0 0 / 0.1)` dark |
| `outline` or `border` on image changes its size | `outline` with `outline-offset: -1px` |
| `fill="#..."` / `stroke="#..."` inside an icon SVG | `currentColor` |
| Mixed icon libraries, or stroke thinner than neighbor text | One library, stroke per the table |
| Filled icon used for every state | Outline default, fill active |
| Directional icon not mirrored in RTL, or checkmark/logo mirrored | Flip only reading-direction icons |
| Translucent surface on translucent surface | Solid layer beneath, or heavier material |
| `backdrop-filter` with no `prefers-reduced-transparency` / `prefers-contrast` fallback | Add solid fallbacks |
| Color transitions plus a theme toggle with no suppression | Section 7 recipe |
| Hover-only styling outside `@media (hover: hover)` | Wrap it |
| Scrollable overlay with no `overscroll-behavior` | `contain` |
| Two ways of doing the same detail in one project | Pick the project convention and unify |

## Credits

Synthesized for Quark from Emil Kowalski's skills (github.com/emilkowalski/skills) and Jakub Krehel's skills (github.com/jakubkrehel/skills), both MIT.
