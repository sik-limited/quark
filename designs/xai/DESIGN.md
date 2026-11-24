---
version: alpha
name: xAI
description: An independent Quark adaptation of publicly observed patterns.
colors:
  primary: '#ffffff'
  on-primary: '#0a0a0a'
  canvas: '#0a0a0a'
  foreground: '#ffffff'
  muted: '#a9adb3'
  border: '#363a3f'
rounded:
  control: 8px
  pill: 9999px
spacing:
  base: 4px
components:
  preview-button:
    height: 40px
    rounded: 9999px
motion:
  source: Quark preview defaults
  strength: 0.35
  spring-stiffness: 480
  spring-damping: 30
---

# xAI — Quark design reference

## Scope and provenance

This is an independent Quark reference compiled from publicly visible product pages and the official sources below. It is not affiliated with or endorsed by the company. Values in this document are observations, not verified official tokens. Brand names and marks remain the property of their owners. The detail-page logo in Quark is a text identifier; the sidebar uses a separate brand symbol.

## Design principles

- **Restrained monochrome** — Build hierarchy with dark surfaces, white text and hairlines.
- **Sparse typography** — Separate large display text from tracked monospace labels.
- **Hairline separation** — Use surface differences and borders instead of shadows.

These are Quark's synthesis of the independent analysis, not a quoted official list of corporate principles.

## Color, type, layout, controls and states

- Palette: canvas `#0a0a0a`, white `#ffffff`, body `#dadbdf`, reference muted `#7d8187`, hairline `#212327`, soft `#1a1c20`, card `#191919`; orange `#ff7a17` and violet `#7c3aed` are sparing illustrative accents. Quark lightens the muted and border preview tokens for a practical dark preview; they are not official values.
- Typography: reference names Universal Sans and Geist Mono; proprietary fonts are not bundled. Display 96/72/48/32/20px, body 18/16/14px, monospace labels 14/12px with expanded tracking. Use licensed faces or fallbacks.
- Layout: 4px-based spacing, 24px card padding and 64px section rhythm. The reference inconsistently mentions 720px and 768px as mobile reflow; treat its breakpoints as approximate, test content, and avoid presenting them as official tokens. Desktop example uses two columns at 1024px and above.
- Controls: a rare white filled primary and transparent white outline pills, a dark soft input with 8px corners, and charcoal 8px cards. Quark uses a 40px button height to make an adaptable example; the reference does not specify a universal button height. Preserve visible focus and contrast on every surface.
- Depth: the reference emphasizes flat surfaces, 1px hairlines and little or no shadow. Its generated illustrative examples mention modal/toast shadows, which conflict with the general rule; use context and do not claim either as an official token.
- Writing: no public xAI product UX-writing system was verified. Brief headings and action labels here are observed/reconstructed examples. Official xAI brand guidelines concern marks and Grok content attribution, not a general UX writing style guide.

## Writing experience

- **Short, specific headings** — Examples draw on concise headings observed in the independent reference, not a verified official product writing guide. Example · Explore features / Read the docs
- **Attribution and marks** — Official brand guidance covers attribution of Grok-generated content and trademark use; consult it in that context. Before use · Check the relevant asset terms

## Customization and implementation

Use semantic colors and adapt controls to the product instead of copying domain-specific copy. Expose default, hover, focus, pressed, disabled, loading and error states where applicable. Preserve label associations, keyboard navigation, readable contrast and reduced-motion preferences. The Quark adapter applies common tokens and a subset of shapes, not every reference component or official implementation. Verify font licensing and brand-asset restrictions.

## Sources

- [xAI · Brand guidelines](https://x.ai/legal/brand-guidelines)

## Quark motion settings

These values are adjustable Quark preview defaults, not published brand motion tokens. They apply to Quark's spring-based rating and character-limit examples; other motion is governed separately by the transition duration and component-specific rules. Preserve explicit documented brand feedback, and always honor the operating system's reduced-motion preference.

- `--q-motion-strength`: `0.35` (0 disables these animations; 1 is the reference amplitude).
- `--q-spring-stiffness`: `480` (relative to the reference baseline of 520).
- `--q-spring-damping`: `30` (relative to the reference baseline of 16).
- `--q-duration`: Quark's separately editable transition duration; setting it to 0 also disables these spring animations.
