---
name: accessibility
description: Build and review semantics, focus, keyboard, targets, forms, announcements, reduced motion and zoom against WCAG 2.2 AA. Use when building or reviewing interactive components, dialogs, menus, tabs, forms, toasts, drag interactions, focus styles or anything keyboard- or screen-reader-facing.
license: MIT
---

# Accessibility

Use this when building or reviewing any interactive component. Review in two walks: keyboard-only (every flow completes without a mouse), then screen reader (every control announces a name, a role and its state). When unsure, take the platform default over a custom rebuild, and remove ARIA rather than add it.

A finding cites a WCAG 2.2 Level A/AA criterion or a concrete task an assistive-technology user cannot complete. AAA criteria, APG conventions and the 44px target are recommendations, never blockers. Contrast measurement and color fixes belong to `design-tokens`; sizing and input font size to `typography`; spacing between targets to `layout`; label and error wording to `interface-copy`; animation recipes to `animate`.

## 1. Native first

1. Use the native element: `<button>` for actions, `<a href>` for navigation (keeps Cmd/Ctrl/middle-click), `<input>`, `<select>`, `<dialog>`, `<details>`. Never `<div onClick>`.
2. Do not change native semantics. No `role="button"` on `<button>`, no `role="menu"` on site navigation (that is `<nav>` plus a list).
3. A role promises the full keyboard model, states and behavior. If you cannot deliver it, drop the role.
4. Every interactive element has an accessible name. `aria-labelledby` or `aria-describedby` pointing at a missing id silently yields nothing.
5. A custom control with no native option needs `role`, `tabindex="0"`, Enter and Space handlers, and state attributes. That is always more code than the native element.

Page-level structure (landmarks, one `<h1>`, skip link to `<main id>`, route-change focus and `document.title`, captions for video) is outside a component library; apply it at the app shell.

## 2. Disabled vs aria-disabled

| Need | Use | Behavior |
| --- | --- | --- |
| Genuinely unavailable | native `disabled` | Leaves tab order, blocks activation, matches `:disabled`, skips form submission |
| Must stay discoverable/focusable (reason shown, tooltip, pending submit) | `aria-disabled="true"` | Announces state only. You must block click, keyboard and submit in the handler and style it yourself (incl. forced-colors) |

- Never set both on one element.
- A natively disabled control gets no hover or focus, so a tooltip on it never opens for keyboard or touch users. Put the reason in visible text beside it, or use `aria-disabled`.
- Never disable submit to gate validation (see Forms).
- Native `disabled` on the focused button drops focus to `<body>`; use `aria-disabled` for a pending submit.
- Disabled controls are exempt from contrast minimums; the reason text is not.
- Keep applied values visible when editing becomes unavailable. For color controls, mute the trigger label and retain the swatch and sample at their selected opacity.

## 3. Focus indicators

- Style `:focus-visible`, not bare `:focus`. Keyboard users get a ring; mouse clicks on buttons do not (text inputs always do).
- Best: keep the browser's ring and only add `outline-offset`. It adapts to forced-colors and backgrounds.
- A custom ring must reach 3:1 against every adjacent color it crosses (fills, surfaces, images, hover/selected states), `currentColor` included (1.4.11). A 2px solid perimeter with 3:1 change of contrast also meets the 2.4.13 AAA target.
- Never `outline: none` without a verified replacement.
- Use `:focus-within` on wrappers that should light up around an inner input (search box with icon).
- A focused element must never be fully hidden by sticky headers, footers or cookie banners (2.4.11). Pad the scroller: `scroll-padding-block-start/end` equal to the sticky chrome height, and `scroll-margin-block-start` on anchor targets.

```css
:focus-visible {
  outline: 2px solid var(--focus-ring, var(--q-accent)); /* extend the project's tokens */
  outline-offset: 2px;
}
@media (forced-colors: active) {
  :focus-visible { outline-color: Highlight; } /* keep system color adjustment */
}
```

`forced-color-adjust: none` freezes authored colors; use only where you verified the control stays perceivable.

## 4. tabindex, roving focus, activedescendant

- `tabindex="0"` joins the tab order (custom interactive elements only). `tabindex="-1"` is for programmatic focus (headings you move focus to, dialog containers, roving members). Positive values are never allowed; fix DOM order.
- Composite widgets (tabs, menus, toolbars, radio groups, listboxes) are one Tab stop. Roving tabindex: active item `0`, the rest `-1`; arrows move both focus and the `0`.
- When DOM focus must stay in a text input (combobox), keep focus on the input and point `aria-activedescendant` at the highlighted option's id; update it on arrows.
- Tab exits the widget; arrows move within it. Escape dismisses whatever opened last (tooltip, then menu, then dialog).

## 5. APG keyboard map

| Widget | Keys and structure |
| --- | --- |
| Button | Enter and Space activate. Toggle: `aria-pressed` |
| Switch | `role="switch"` or checkbox; Space toggles; `aria-checked`; label states the ON meaning |
| Checkbox | Space toggles; mixed state `aria-checked="mixed"` |
| Radio group | One Tab stop; arrows move and select (wrap); Space selects focused |
| Dialog | Tab/Shift+Tab cycle inside, wrapping; Escape closes; focus enters on open, returns to trigger on close |
| Tabs | `tablist/tab/tabpanel`; Left/Right move (wrap), Home/End jump; Tab leaves to the panel. Automatic activation when panels render instantly; manual (Enter/Space) when switching is expensive |
| Menu button | Enter/Space/ArrowDown opens and focuses first item; ArrowUp opens on last; arrows navigate; Escape closes and refocuses the button; typing jumps to a matching item |
| Disclosure / accordion | Header is `<button aria-expanded aria-controls>`; Enter/Space toggle; optional Up/Down/Home/End between headers |
| Combobox | Input `role="combobox" aria-expanded aria-controls aria-activedescendant`; ArrowDown opens/moves; Enter accepts; Escape closes then clears; typing filters |
| Listbox | Arrows move selection; one Tab stop; Home/End; type-ahead; `aria-selected` |
| Slider | Arrows ±step, PageUp/Down ±large step, Home/End min/max; `aria-valuenow/min/max`, `aria-valuetext` when the number is not meaningful |
| Toolbar | One Tab stop, arrows move between items |

Enter in a single-line input submits natively; in `<textarea>` it inserts a newline (Cmd/Ctrl+Enter submits only if you implement it).

## 6. Dialog focus management

- Prefer `<dialog>` with `showModal()`: background becomes inert, Escape works, top layer. Call `.close()` then `trigger.focus()`.
- Custom overlay: `role="dialog" aria-modal="true" aria-labelledby`, `inert` on the background container, rendered outside that container (portal or sibling; `inert` on an ancestor disables the dialog too).
- On open, focus the first focusable (or `[autofocus]`). For destructive confirmations focus the least destructive action. Non-modal popovers do not trap focus.
- On close, return focus to the trigger, or the nearest logical container if the trigger is gone.
- A modal must have a visible close button; Escape alone is not enough on touch.

## 7. Target size and hit area

| Standard | Minimum |
| --- | --- |
| WCAG 2.5.8 AA | 24x24 CSS px, or an exception |
| Recommended touch | 44x44 px (Apple 44pt, Material 48dp), heuristic not blocker |

Exceptions to check before reporting: spacing (a 24px circle centered on the target hits no other target; 20px targets need 4px gaps), equivalent control elsewhere, inline text links, user-agent default, essential.

- The visible element may stay small; the hit area must be large. Anything that looks clickable is clickable across its visual extent.
- Extend with a pseudo-element on the `<button>` or the `<label>` wrapping the input, never on `<input>` (form controls do not render `::before/::after` reliably).
- Where an extended area would overlap another control, shrink it to the largest non-colliding size.
- Decorative layers over controls (glows, scrims, full-bleed `::after`) get `pointer-events: none`. Keep pointer events on intentional dismiss scrims.

```css
.checkbox-label { position: relative; }
.checkbox-label::after {
  content: "";
  position: absolute;
  top: 50%; left: 50%;               /* physical centering is direction-independent */
  width: 44px; height: 44px;
  transform: translate(-50%, -50%);
}
.icon-button { min-width: 44px; min-height: 44px; display: inline-grid; place-items: center; }
```

## 8. Gestures and drag alternatives

- Every drag needs a single-pointer alternative reaching the same result: move up/down buttons on sortable rows, a "Move to" menu on kanban cards, keyboard arrows on sliders (2.5.7).
- Every multipoint or path-based gesture (pinch, swipe) needs a single-pointer alternative (2.5.1).
- `touch-action: none` only on the surface that implements its own pan/zoom/drag; never page-level. A horizontal carousel uses `touch-action: pan-y`.
- Gesture tracking is direct manipulation and stays under reduced motion; remove momentum flings, bounce and decoration (see section 12).
- Hover is never the only way to reveal content or actions. Gate hover styles with `@media (hover: hover) and (pointer: fine)`; touch gets feedback through `:active`.
- Controls (buttons, tabs, chips, drag handles) get `user-select: none`; content text must stay selectable.

## 9. Forms

- Every input has `<label for>` or a wrapping `<label>`; a placeholder is never a label. Label and control share one hit target (no dead zone between checkbox and text).
- Required: native `required` plus a visible indicator explained once ("* required").
- Never block paste (passwords, OTP). Use a real `<form>`, a descriptive `name`, and no fake inputs so managers and 2FA autofill work.
- `autocomplete` on fields about the user is required (1.3.5); section prefix where relevant (`shipping street-address`).

| Field | `autocomplete` |
| --- | --- |
| Name | `name`, `given-name`, `family-name` |
| Email / phone | `email` / `tel` |
| Address | `street-address`, `address-line1`, `postal-code`, `country` |
| Card | `cc-number`, `cc-exp`, `cc-csc`, `cc-name` |
| Login / signup | `username` + `current-password` / `new-password` |
| 2FA code | `one-time-code` |

| Input | `type` / `inputmode` |
| --- | --- |
| Email, URL, phone | `type="email"`, `"url"`, `"tel"` |
| OTP, PIN, card number | `type="text" inputmode="numeric"` (no spinner) |
| Money, decimals | `type="text" inputmode="decimal"` |
| True quantity | `type="number"` |

Add `spellcheck="false"` on emails, codes, usernames. Inputs stay at 16px+ on mobile (`typography`).

**Validation and errors**
- Accept free text, validate after; never filter keystrokes. Trim before validating.
- Validate on submit, not by disabling submit until valid.
- Failing field: `aria-invalid="true"` and `aria-describedby` pointing at inline error text; remove both when fixed. Error has text or an icon, never a red border alone (1.4.1). Focus the first invalid field on submit; that focus move is the announcement.
- Pending submit: keep it enabled until the request starts, then show a spinner beside the original label ("Save" plus spinner, not a bare spinner), set `aria-disabled="true"`, ignore repeat activations. Success goes to a polite region; form-level errors to `role="alert"`.
- A re-render or hydration must never reset typed values or move focus out of a field. Warn before navigating away with unsaved changes.

## 10. Accessible names

- Precedence: `aria-labelledby` > `aria-label` > native label/text/`alt` > `title`. Prefer visible text over `aria-label` (it drifts and is mishandled by translation tools).
- Icon-only buttons: `aria-label` naming the action ("Delete", not "Trash icon") and the icon `aria-hidden="true"`.
- Label in Name (2.5.3): the accessible name must start with / contain the visible text. Button "Send" with `aria-label="Submit message"` breaks voice control.
- `aria-label` on a plain `div`/`span` is ignored; do not use it.
- `aria-hidden="true"` for decorative icons and visual duplicates; never on or above a focusable element.
- Links make sense out of context; distinct destinations get distinct names.
- Alt by purpose: decorative `alt=""` (present but empty; missing is worse, readers say the filename); informative describes meaning; functional describes the action ("Search"); image of text gives the exact text; complex chart gets a short summary plus data as text/table nearby.
- SVG: decorative `aria-hidden="true"`; meaningful `role="img"` with `aria-label` or `<title>` via `aria-labelledby`.
- Status needs a second cue beyond color (icon, text, underline) (1.4.1).
- Contrast thresholds: text 4.5:1; large text (24px, or 18.67px bold) 3:1; UI boundaries/states/graphics 3:1 (1.4.3, 1.4.11). Measure and fix in `design-tokens`.
- Brand names and code tokens: `translate="no"`.

## 11. Announcing changes

Take the first match:

1. Focus moves there anyway (opened modal, first invalid field): nothing extra.
2. Tied to one control (field error, character count): `aria-describedby` on it.
3. Non-urgent, untied (toast, "Saved", result count, loading): polite live region, `role="status"`.
4. Urgent, untied (form-level failure, session expiry): `role="alert"`, nothing else.

- Repeated polite updates need a stable, empty region in the DOM before its text changes; an inserted region arriving with content is announced inconsistently. Dynamically inserted alerts vary; test target screen readers.
- Default to polite; assertive interrupts. Keep messages short and self-contained (`aria-atomic` rereads all of it).
- Never move focus to a toast.
- Loading: `aria-busy="true"` on the region, announce "Loading…", then the outcome ("Loaded, 12 results").
- Hide-visually text uses `.sr-only`, never `display:none`:

```css
.sr-only {
  position: absolute; width: 1px; height: 1px; padding: 0; margin: -1px;
  overflow: hidden; clip: rect(0 0 0 0); clip-path: inset(50%);
  white-space: nowrap; border: 0;
}
```

Use it for "Opens in new tab", table captions, icon labels where `aria-label` is not an option. Use 1px, not 0 (some readers skip zero-size boxes).

## 12. Reduced motion

Wrap motion in `@media (prefers-reduced-motion: no-preference)` so the static version is the default. Reduce vestibular triggers, keep feedback.

| Treatment | What |
| --- | --- |
| Remove | Parallax, autoplaying video/GIF, looping decoration, spinning or large cross-screen movement |
| Replace | Slide/scale/zoom becomes an opacity cross-fade; smooth scroll becomes an instant jump; auto-rotating carousels start paused |
| Keep | Spinners and progress, instant state changes (hover color, focus ring), brief functional feedback (a press state as color/opacity; its scale is gated like other motion), direct-manipulation tracking |

```css
.panel { opacity: 0; transition: opacity 150ms ease; }
.panel[data-open] { opacity: 1; }
@media (prefers-reduced-motion: no-preference) {
  .panel { transform: translateY(8px); transition: opacity 200ms ease, transform 200ms var(--ease-out); /* token from `animate` */ }
  .panel[data-open] { transform: none; }
}
```

## 13. Timed UI and toasts

- Moving, blinking or scrolling content lasting more than 5s needs a visible pause/stop/hide control (2.2.2); auto-updating content needs one at any duration (a frequency control counts).
- Auto-dismiss only low-stakes confirmations. A toast carrying an action, an error or needed information stays until dismissed.
- A toast that must time out lasts 5s or more and pauses on hover and focus.
- Never put the only path to an action (undo link) in a vanishing element.
- Never autoplay with sound (1.4.2).

## 14. Zoom and reflow

- Text survives 200% resize with no loss of content or function (1.4.4).
- Reflow at 320 CSS px width (400% zoom on 1280px) with vertical scroll only (1.4.10). Genuinely 2D content (tables, maps, code) scrolls inside its own container.
- Text containers use `min-height`, not `height`. Never `maximum-scale=1` or `user-scalable=no`; fix iOS zoom with 16px inputs.
- Write media queries in `rem`/`em` so larger default font sizes switch to the narrow layout. Breakpoint placement is in `layout`.
- Honor forced-colors: borders on controls, no color-only state, SVG icons use `currentColor`.

## Before you finish

| Pattern | Fix |
| --- | --- |
| `outline: none` with no `:focus-visible` replacement | Restore browser ring or a verified 3:1 custom one |
| `<div onClick>`, `<span onClick>` | `<button>` or `<a href>` |
| `role="button"`/`"tab"` without key handlers | Native element or full APG key map |
| Positive `tabindex` | Fix DOM order, use `0` |
| `disabled={!isValid}` on submit | Keep enabled, validate on submit |
| Tooltip on a natively disabled control | Text beside it, or `aria-disabled` |
| Region mounted with its text (`{msg && <div role="status">}`) | Render empty region first, update text |
| `aria-live="assertive"` on a success toast | `role="status"` |
| `<img>` with no `alt` | `alt=""` or purpose-based text |
| `aria-label` omitting the visible label | Start the name with the visible text |
| `aria-hidden` on or above a focusable element | Remove, or make the subtree `inert` |
| Drag library or `onDragStart` with no button path | Add single-pointer alternative |
| Hit area under 24px or overlapping neighbors | Pseudo-element extension, shrunk to avoid collisions |
| Glow/scrim over controls without `pointer-events: none` | Add it |
| Color is the only error/status cue | Add icon or text |
| Motion with no `prefers-reduced-motion` handling | Gate behind `no-preference`, add fade fallback |
| Toast with action/error that auto-dismisses | Persist until dismissed |
| `height:` on text box, `user-scalable=no` | `min-height`; remove the meta restriction |

Severity: HIGH when a task is blocked or content is hidden from assistive tech (missing name, missing focus indicator, pointer path with no keyboard path, motion ignoring reduced motion, loss at 320px/200%, color-only meaning). MEDIUM makes an interaction meaningfully harder. LOW is isolated polish. Report checks you could not run as not verified.

## Credits

Synthesized for Quark from Emil Kowalski's skills (github.com/emilkowalski/skills) and Jakub Krehel's skills (github.com/jakubkrehel/skills), both MIT.
