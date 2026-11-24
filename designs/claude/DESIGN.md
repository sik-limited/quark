---
version: alpha
name: Claude
description: An independent Quark adaptation of publicly observed patterns.
colors:
  primary: '#cc785c'
  on-primary: '#ffffff'
  canvas: '#faf9f5'
  foreground: '#141413'
  muted: '#6c6a64'
  border: '#e6dfd8'
rounded:
  control: 8px
  pill: 9999px
spacing:
  base: 4px
components:
  preview-button:
    height: 40px
    rounded: 8px
motion:
  source: Quark preview defaults
  strength: 0.55
  spring-stiffness: 450
  spring-damping: 26
---

# Claude — Quark design reference

## Scope and provenance

This is an independent Quark reference compiled from publicly visible product pages and the official sources below. It is not affiliated with or endorsed by the company. Values in this document are observations, not verified official tokens. Brand names and marks remain the property of their owners. The detail-page logo in Quark is a text identifier; the sidebar uses a separate brand symbol.

## Design principles

- **Warm editorial composition** — Separate cream canvas, coral actions and dark product surfaces by role.
- **Type-led hierarchy** — Pair large serif headlines with readable sans-serif body copy.
- **Color before shadow** — Distinguish surfaces with color and borders before adding shadows.

These are Quark's synthesis of the independent analysis, not a quoted official list of corporate principles.

## Color, type, layout, controls and states

- Palette: coral `#cc785c`, active `#a9583e`, cream canvas `#faf9f5`, ink `#141413`, body `#3d3d3a`, muted `#6c6a64`, border `#e6dfd8`, soft surface `#f5f0e8`, card `#efe9de`, dark `#181715`; teal and amber are secondary illustrative accents.
- Typography: independent analysis identifies Copernicus / Tiempos Headline for display, StyreneB / Inter for body and JetBrains Mono for code. Commercial faces are not bundled; Quark uses system fallbacks. Display sizes 64/48/36/28px; body 16/14px.
- Layout: 96px section rhythm, max content about 1200px in the example. Reference mobile <768px stacks hero, features and pricing; tablet 768–1024px uses two columns; desktop restores the full grid.
- Controls: primary coral and secondary cream buttons 40px high with 8px radius; input 40px; feature and code cards 12px radius. Focus is a visible coral ring. CTA, tabs, chips, inputs and feedback should preserve keyboard and disabled states.
- Depth: mostly flat color blocks with thin borders; darker mockup cards contrast with cream surroundings. Icon buttons are illustrated at 36px; provide an adequate hit area in your application.
- Official scope: Anthropic's published brand-styling skill example uses colors including `#141413`, `#faf9f5`, `#d97757` and Poppins/Lora. It is a brand styling example, not evidence of Claude product tokens or an official UX-writing guide. Its colors differ from the independent reconstruction and are not substituted silently.

## Writing experience

- **Name the action** — Observed buttons name the next action briefly. These examples are reconstructed by Quark. Example · Get started / Read documentation
- **Lead with purpose and result** — Introductory copy explains the purpose and expected outcome first. Example · Organize documents and find what you need.

## Customization and implementation

The observed coral `#cc785c` with white text does not meet a 4.5:1 small-text contrast ratio; choose a darker action color or another accessible foreground in production. Use semantic colors and adapt controls to the product instead of copying domain-specific copy. Expose default, hover, focus, pressed, disabled, loading and error states where applicable. Preserve label associations, keyboard navigation, readable contrast and reduced-motion preferences. The Quark adapter applies common tokens and a subset of shapes, not every reference component or official implementation. Verify font licensing and brand-asset restrictions.

## Sources

- [Anthropic · Brand styling skill example](https://claude.com/resources/articles/how-to-create-skills-key-steps-limitations-and-examples)

## Quark motion settings

These values are adjustable Quark preview defaults, not published brand motion tokens. They apply to Quark's spring-based rating and character-limit examples; other motion is governed separately by the transition duration and component-specific rules. Preserve explicit documented brand feedback, and always honor the operating system's reduced-motion preference.

- `--q-motion-strength`: `0.55` (0 disables these animations; 1 is the reference amplitude).
- `--q-spring-stiffness`: `450` (relative to the reference baseline of 520).
- `--q-spring-damping`: `26` (relative to the reference baseline of 16).
- `--q-duration`: Quark's separately editable transition duration; setting it to 0 also disables these spring animations.
