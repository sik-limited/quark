---
name: design-tokens
description: "Build, structure and audit a design-token system: token tiers and naming, spacing, radius, elevation and z-index, border and motion tokens, theming and modes, plus OKLCH color ramps, contrast, dark mode and design-to-token mapping. Use when creating or restructuring tokens, adding a theme or dark mode, choosing a scale, fixing contrast, auditing hard-coded values, or mapping a design value to a token."
license: MIT
---

# Design Tokens

## Use this when

Defining or renaming tokens, building a scale (space, radius, shadow, z-index, motion, color), adding a theme or appearance, deciding whether a value deserves a token, fixing contrast, or translating a design value into code.

Scope limits: whether a pair must pass belongs to `accessibility`; surfaces, shadow craft and concentric radius to `interface-polish`; spacing relationships to `layout`; type scale values to `typography`; easing and duration values to `animate`; the full design-translation workflow to `design-to-code`. "Lightness" below is OKLCH `L` (0-1).

Never report a contrast value you did not measure, and never estimate a color you can compute. Notation choice, a tinted neutral and gradient interpolation space are project decisions, not findings. A broken role mapping, a failing pair, an out-of-gamut step and a hard-coded value where a token exists are findings.

## 1. Match the project first

Reuse existing tokens, names and notation. A consistent hex system beats hex with stray `oklch()`; do not bulk-convert unless asked. Extend the project's tokens for anything missing; do not invent names that fight its grammar (in Quark, do not coin new `--q-*` names ad hoc). For a new system write `oklch(L C H)` (chroma about 0-0.4, alpha with a slash) and use a color library or calculator for ramp math.

## 2. Token architecture (every category)

Tiers:

- **Primitive**: a raw value on a scale (`--space-4: 16px`, `--blue-600`, `--radius-3: 12px`). Never used in a component.
- **Semantic**: a job pointing at a primitive (`--space-inset`, `--color-accent-solid`, `--radius-control`). Components consume only this tier.
- **Component** (`--button-radius`): only when one component truly diverges from the semantic role. One is an exception; twenty means the semantic tier lacks roles.
- Themes, modes and density repoint the semantic tier (or the base unit). Primitives and components stay untouched.
- Use a token only in its role. A border token as a text color works until borders lighten. If the role has no token, add one.

Naming grammar: `--{ns}-{category}-{role}-{variant}-{state}`. Drop segments that add nothing (`--q-gap`, `--color-bg-surface-hover`).

- Name the role, not the appearance or first use (`--space-section`, not `--space-24`).
- One word per concept across categories (`text` not fg/ink, `bg` not fill, `border` not stroke, `accent` for the brand). Reserve `primary` for "most prominent of its group" (`text-primary`); never also name the brand `primary`.
- Numbers belong to primitives only (`--color-text-2` is unguessable).

Derive instead of duplicate. One base value feeds many; change the base and everything follows. Quark does exactly this:

```css
:root {
  --q-space: 4px;                              /* the only spacing input */
  --q-gap: calc(var(--q-space) * 2);           /* 8px: between items */
  --q-control-px: calc(var(--q-space) * 4);    /* 16px */
  --q-control-py: calc(var(--q-space) * 2.5);  /* 10px */
  --q-panel-padding: calc(var(--q-space) * 5); /* 20px */
}
```

Add a token or reuse one:

| Situation | Decision |
| --- | --- |
| Same value in 3+ places with the same meaning | Add a token for the role |
| A real role with one use today (focus ring, scrim, modal layer) | Add it: roles are tokens even at one use |
| A one-off number for one element | Keep local, not a token |
| A value that only exists to match one design number | Do not add it. Use the nearest token, flag it as rounded |
| A value derivable from an existing token via `calc()` | Derive, do not add |

Never add a token to hit one design value. Fewer, well-named tokens beat many near-duplicates.

## 3. Spacing scale

- One base unit; 4px is the common family (Quark default 4px, adjustable 2-8px). Every space value is a multiple of it via `calc()`.
- Ladder of 7-9 steps, roughly geometric at the large end. Suggested starting point: `1, 2, 3, 4, 6, 8, 12, 16` times the base (4, 8, 12, 16, 24, 32, 48, 64px). Fine steps (4/8/12) for inside components, coarse steps for between sections. No 5px, 7px, 13px.
- Semantic roles from the ladder: inline gap, control padding, panel padding, stack gap, section gap.
- Inside vs between: space between groups is at least 2x the space within them (see `layout`). The tokens must make this hard to get wrong: the section gap role is at least double the item gap role.
- Control padding is a pair, horizontal larger than vertical (Quark: 4 and 2.5 units).
- Density modes (compact, comfortable) change the base unit or a small multiplier, never each value. Compact: base 3px; comfortable: 4px; spacious: 5px (Quark's `clay` preset uses 5). If density requires editing individual tokens, the scale is not derived.
- Compact density must not shrink a target below 24px (see `accessibility`).

## 4. Radius scale

- Few steps: 4-5 tokens. Example: none 0, small (inputs, chips), medium (controls, cards), large (panels, sheets), full.
- Derive steps from one base (Quark `--q-radius`, 0-24px, default 10). Suggested starting multipliers: small = base x 0.5, large = base x 1.5, so one edit changes the theme's character.
- `full` / pill is its own token (`9999px`, or `calc(infinity * 1px)`), not "the biggest number on the scale": scaling the base must not turn a square theme's avatars into squares or a round theme's pills into blobs.
- Nested radius follows the concentric rule: inner = outer minus the padding between. The arithmetic and the cutoff belong to `interface-polish`; the token system supplies the base and `calc()`, not a second set of values.

## 5. Border width

- One base (Quark `--q-border-width`, 0-3px, default 1px). 1px hairlines for separators and control edges; 2px for focus rings and strong emphasis.
- Add at most two semantic widths (default, strong). Focus ring width is its own role, not `border-width` (a theme at 0 must not remove the focus indicator).

## 6. Elevation and z-index

Shadow levels carry meaning, not decoration:

| Level | Meaning | Use |
| --- | --- | --- |
| flat (0) | In the page | Default surfaces, separated by border or surface tone |
| raised (1) | Sits above content | Cards, sticky bars |
| overlay (2) | Floats, dismissible | Dropdowns, popovers, tooltips |
| modal (3) | Blocks the page | Dialogs, drawers |

- Higher levels: larger blur and offset, slightly higher alpha, same light direction. Derive from one base (Quark `--q-shadow`, 0-30%, default 8%).
- Shadows lose meaning on dark surfaces. In dark mode shadows get weaker or are dropped; communicate elevation by lifting the surface tone (higher level, lighter surface) and adding a subtle border. Raise the shadow token (Quark `midnight` uses 20%) only for separation against busy backgrounds.
- Shadows are tokens, never inline; craft rules are in `interface-polish`.

z-index scale with named layers, one declaration site (suggested starting values; keep the order, adapt the numbers):

```css
:root {
  --z-base: 0;
  --z-dropdown: 100;
  --z-sticky: 200;
  --z-overlay: 300;   /* scrims */
  --z-modal: 400;
  --z-toast: 500;
  --z-tooltip: 600;
}
```

- Never use an arbitrary z-index (`9999`, `z-index: 3` to "fix" one overlap). Pick a named layer or fix the stacking context.
- Gaps of 100 leave room to insert; `+1` offsets only inside a component's own context.
- Use `isolation: isolate` on component roots so internal `z-index` does not leak into the global scale.
- Tooltips and toasts sit above modals so a tooltip on a modal control stays visible.

## 7. Typography tokens

- Define role tokens (display, heading, body, label, caption, code), each bundling size, line-height, weight and tracking. Components take a role, never loose size and line-height pairs. Scale values, ratios and tracking rules belong to `typography`; do not duplicate them here.
- Derive from one base (Quark `--q-font-size`, 12-18px, default 13). Express steps as `calc()` or a ratio from the base so a global size change keeps hierarchy.
- Line-height is unitless. Family, weight and numeric features are tokens too; no weight literal in a component.

## 8. Motion tokens

- Tokens for duration (Quark `--q-duration`, 0-500ms, default 175) and easing (`--ease-out`, `--ease-in-out`, `--ease-drawer`). The values, the pairing with interaction type, frequency gating and reduced-motion rules are owned by `animate`; reference them and do not fork a parallel set.
- Duration roles scale from the base: instant, fast, base, slow. Duration 0 must leave components fully correct without transitions.
- No `transition: all`; list properties, and take duration and easing only from tokens.

## 9. Theming and modes

- A theme swaps semantic values only. If a theme needs a new component rule, a semantic role is missing.
- Modes (light, dark, high contrast per appearance, brand) are override sets on the semantic tier only.
- One mechanism per axis: `:root` defaults, `[data-theme]` or class for explicit choice, media queries for initial values only.
- Every theme defines every semantic token or inherits a tested default. Validate imported themes before applying them as CSS (Quark rejects out-of-range numbers and non-hex colors).
- Test extremes: zero radius, zero border, max space, smallest and largest font, duration 0, shadow 0.

How a Quark brand theme maps (`BrandTheme`):

| Theme field | Maps to |
| --- | --- |
| `colors` (six roles: `accent`, `onAccent`, `surface`, `text`, `muted`, `border`) | `--q-accent`, `--q-on-accent`, `--q-surface`, `--q-text`, `--q-muted`, `--q-border` (merged over the current tokens) |
| `typography.scale` (`{ name, size, line }` list) | Reference only; does not set `--q-font-size`. Map it to your type role tokens (`typography`) |
| `layout.spacing` (rules with a `value`, or `heights` plus optional `radius`) | Reference only; not derived from `--q-space`. Map to spacing and radius roles |
| `pattern.button` (`height`, `radius`, `pressScale`, `focusRing`) | Reference spec for the control tier; not written to `--q-*` tokens |

Only `colors` is applied as tokens; a brand theme changes values, not structure. Quark's token presets (separate from brand themes) show the numeric range: `cobalt` (radius 6), `clay` (radius 16, space 5), `midnight` (dark surface, shadow 20).

## 10. Color: how many ramps

| Ramp | Count | Notes |
| --- | --- | --- |
| Neutral | 1 | Carries 80-90% of the UI: backgrounds, borders, body text. At least as many steps as the accent |
| Accent | 1 | The brand hue: interactive and selected states. A second accent only when two things must be told apart at a glance |
| Status | 0-4 | `danger`, `warning`, `success`, `info`; only those the product renders. Four roles usually suffice: bg, border, solid, text |

Gray neutral is a fine default; a tint (chroma 0.005-0.02 at the accent hue) is optional but must hold across the ramp.

## 11. Ramp step rules

- Lightness changes steadily in OKLCH `L` (HSL lightness bunches). Hue is constant end to end.
- Chroma peaks mid-ramp and falls near both ends; the lightest and darkest steps are nearly neutral. Neither end reaches pure white or black.
- Steps are finer at the light end: about 0.04-0.05 `L` apart for 50-200, 0.07-0.10 mid-ramp. Adjacent steps closer than 0.03 means too many steps.
- Across hues, match lightness exactly per step and chroma as the same proportion of each hue's own maximum (yellow and cyan peak low; one chroma number makes warning look weak beside danger).
- A brand color goes on the solid-fill step. Pin it only if contractually fixed; otherwise snap it onto the ramp. If white on it fails 4.5:1, it is still the brand color: use a darker step for fills rather than quietly darkening it.

## 12. Every step has a job (12 roles)

| Role | Radix step | Tailwind step |
| --- | --- | --- |
| Page background | 1 | 50 |
| Subtle background | 2 | 50 |
| Component background | 3 | 100 |
| Component hover / active | 4-5 | 200 |
| Subtle border, separator | 6 | 200 |
| Interactive border | 7 | 300 |
| Strong / hovered border, focus ring | 8 | 400 |
| Solid fill | 9 | 600 |
| Solid fill hover | 10 | 700 |
| Low-contrast text | 11 | 700 |
| High-contrast text | 12 | 900 |

Do not generate steps no role uses. Radix steps are defined by role (step 9 is the fill in light and dark); Tailwind steps by lightness, so the mapping inverts in dark mode and must live in the semantic tier. Prefer role-defined steps for new systems. Solid fill sits on 600 because white on most 500 hues fails 4.5:1.

Color role inventory (complete when each has a token):

| Group | Roles |
| --- | --- |
| Surfaces | page, surface, raised (menus, popovers), sunken (inputs), overlay scrim |
| Text | primary, secondary, disabled, inverse, on-accent |
| Borders | subtle, default, strong, focus ring, separator (distinct from border) |
| Accent | subtle bg, border, solid, solid hover, text |
| Status | per shipped status: subtle bg, border, solid, text |

Color naming applies the section 2 grammar: neutral `--color-{bg|text|border}-{variant}-{state}`, accent and status `--color-{accent|danger|...}-{subtle|border|solid|text}-{state}`.

```css
:root {
  --blue-600: #2563eb;                      /* primitive */
  --neutral-200: #e5e7eb;
  --color-accent-solid: var(--blue-600);    /* semantic */
  --color-border: var(--neutral-200);
}
.btn { background: var(--color-accent-solid); border: 1px solid var(--color-border); }
```

| Bad name | Why | Use |
| --- | --- | --- |
| `--color-blue-button` | Appearance at semantic tier | `--color-accent-solid` |
| `--color-light-gray` | Lies in dark mode | primitive `--neutral-200` |

## 13. Quark's semantic tokens

Quark exposes six semantic colors plus numeric tokens; they are a small semantic tier. Use them as the example mapping and extend the project's tokens for anything missing.

| Quark token | Role | Neutral grammar equivalent |
| --- | --- | --- |
| `--q-surface` | Panels, cards, controls background | `--color-bg-surface` |
| `--q-text` | Primary text | `--color-text-primary` |
| `--q-muted` | Secondary/helper text | `--color-text-secondary` |
| `--q-border` | Edges and separators (width via `--q-border-width`) | `--color-border` |
| `--q-accent` | The one filled action, selected state, focus | `--color-accent-solid` |
| `--q-on-accent` | Text/icons on an accent fill | `--color-text-on-accent` |

```css
.primary { background: var(--q-accent); color: var(--q-on-accent); border: 0; }
.secondary { background: var(--q-surface); color: var(--q-text);
  border: var(--q-border-width) solid var(--q-border); }
.hint { color: var(--q-muted); }
```

Components use these, never raw hex. `--q-on-accent` must pass contrast against `--q-accent` and `--q-muted` against `--q-surface`. Hover, disabled and status variants come from the project's extended tokens, not alpha hacks on `--q-accent`.

## 14. One meaning, one filled action

- Use a color for one purpose everywhere; hues within 15 degrees read as the same color. If the accent means interactive, an interactive element rendered neutral is equally misleading.
- Status hues sit at least 15 degrees of OKLCH hue from the accent. If convention leaves no room (red brand, red danger), give destructive actions a distinct treatment (outline plus icon).
- Never let color alone carry meaning; pair with icon or label (see `accessibility`).
- Exactly one filled primary action per view; peers stay neutral. Put the color on the background, not the label: accent text on a neutral button reads as a link. Selected tabs or segments may use accent on glyph and label (state, not emphasis).

## 15. Contrast

Measure foreground against the background it actually renders on (nearest painting ancestor), not the page.

| Content | WCAG AA | WCAG AAA | APCA (abs Lc) |
| --- | --- | --- | --- |
| Body text | 4.5:1 | 7:1 | 75 (prefer 90) |
| Labels, headlines | 4.5:1 | 7:1 | 60 |
| Large text (24px, or 18.67px bold) | 3:1 | 4.5:1 | 45 (36px regular / 24px bold) |
| Fine or outline icons | 3:1 | - | 45 |
| Solid icons, UI components | 3:1 | - | 30 |
| Dividers, discernible only | - | - | 15 |

Disabled controls and logos are exempt; aim for Lc 30 on disabled/placeholder anyway. Lc is signed (negative = light on dark); compare the absolute value. Without a stated standard, report WCAG 2 and add APCA.

Fixing a failing pair (only when asked; colors are a design decision): change lightness first, holding hue and chroma, then remeasure. A mid-lightness background (L near 0.75) caps even black text near Lc 60, so change the background. Pushing lightness can leave sRGB: reduce chroma. Pick text polarity per pair with the project's metric (WCAG's neutral crossover is near L 0.56; APCA and WCAG can disagree).

Also check: every pair in both appearances; translucent or blurred surfaces against the lightest and darkest content behind them; `color-mix()`, relative color and alpha resolved at render time (measure the result, or use solid tokens for anything carrying text); text over images by the worst region or a scrim.

## 16. Dark mode, contrast preference, switching

- Dark is not the light ramp reversed. Swap semantic roles first, then tune: lower accent chroma a step or two (vivid on white becomes neon on black), widen separation at the dark end, remeasure every pair. Elevation in dark comes from lighter surfaces (section 6).
- Use one switching mechanism. `prefers-color-scheme` alone when there is no toggle; a class or `data-theme` once users can override (the media query then sets only the initial value); `light-dark()` collapses both values into one declaration but requires `color-scheme: light dark` (a class toggle must set `color-scheme` too). Never half-themed.
- Give each appearance its own increased-contrast value; one shared `prefers-contrast: more` block darkens on dark backgrounds and lowers contrast. The increased variant widens the gap to its background by at least 0.15 `L`; remeasure (Lc 90 for body).

```css
:root {
  color-scheme: light dark;
  --color-bg: light-dark(#fff, #0f172a);
  --color-accent-solid: light-dark(var(--blue-600), var(--blue-400));
}
@media (prefers-contrast: more) {
  :root { --color-accent-solid: light-dark(var(--blue-900), var(--blue-200)); }
}
```

## 17. Gamut and gradients

- Every sRGB color exists in Display P3, not the reverse. Out-of-gamut colors get clipped, flattening neighbouring steps. Build ramps for sRGB, then add P3 as an enhancement with the sRGB value first:

```css
.success { background: #11ad32; }
@media (color-gamut: p3) { .success { background: oklch(0.65 0.27 145); } }
```

- Gradients: interpolation space is a look. Default `in oklab`; `in oklch` stays vivid but arcs through hues; plain sRGB mutes the midpoint. A gray dead zone between opposite hues: use a polar space or add a middle stop. Large low-contrast gradients band: widen contrast, shrink the area or add subtle noise. Keep text off gradients, or measure the worst region or add a scrim.

## 18. Mapping a design value to a token

`design-to-code` owns the full translation workflow; this is the token step of it.

1. Read the project's tokens first.
2. Bound variable or raw value equal to a token: use that token. Raw value with no token: the nearest existing token, flagged as rounded.
3. If rounding would shift spacing or size by more than 2px, or move a color to a different step, stop and ask.
4. Never add a token, variant or arbitrary value just to hit a number; no raw hex in components. Add a token only through the project's token process, in its notation.
5. Report: element, design value, token used, status (matches / rounded / deviates).

## 19. Token audit

Run in order; report one row per root cause with every location.

1. **Literals in components**: grep for hex, `rgb()`, `oklch()`, bare `px`/`rem` spacing, `border-radius`, `box-shadow`, `z-index`, `transition` durations and `font-size` outside the token layer.
2. **Near-duplicates** (`15px`/`16px`, `#333`/`#2f2f2f`): collapse onto one token.
3. **Unused tokens**: remove, unless a role awaiting a consumer (focus ring, scrim).
4. **Semantic leaking primitives**: a component using `--blue-600`, a semantic token holding a literal, or a semantic name containing a hue, number or component.
5. **Derivation gaps**: values typed separately that should `calc()` from the base.
6. **Theme completeness**: every theme and mode defines every semantic token; test the extremes from section 9.

## Before you finish

| Pattern | Fix |
| --- | --- |
| Hex/`rgb()`/`oklch()`/px literal in a component where a token exists | Use or add the role token |
| Primitive (`--blue-600`) in a component, or hue/number/component in a semantic name | Semantic token named for the role |
| Border token in `color:` or text token in `background:` | Add the missing role |
| Token added to match one design number | Remove; use the nearest token, flag as rounded |
| Spacing values typed separately instead of derived | `calc()` from the base unit |
| Arbitrary `z-index` (`9999`, `3`) | Named layer from the scale |
| Same shadow in dark as light | Weaker shadow, lighter surface, border |
| Focus ring tied to `border-width` | Own focus-ring role |
| Duration/easing literal; `transition: all` | Motion tokens, listed properties |
| Status hue within 15 degrees of accent | Move it, or distinct destructive treatment |
| Dark tokens = light ramp reversed | Lower accent chroma, widen dark end, remeasure |
| One `prefers-contrast: more` block for both appearances | Per-appearance values |
| Text on alpha color (`/50`, `rgba`, `color-mix` with transparent) | Measure rendered result or use a solid token |
| Chroma beyond sRGB with no sRGB fallback | sRGB value first, P3 in `@media (color-gamut: p3)` |

Severity: HIGH when body or control text fails its required ratio, meaning is carried by color alone, a semantic color is used against its meaning, or a theme leaves a semantic token undefined. MEDIUM for noticeable theme, token, scale or gamut failures and hard-coded values where tokens exist. LOW for isolated polish. Group findings by violated principle, one row per root cause with every location; mark unmeasured checks as not verified.

## Credits

Synthesized for Quark from Jakub Krehel's skills (github.com/jakubkrehel/skills), MIT.
