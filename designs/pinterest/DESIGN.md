---
version: alpha
name: Pinterest
description: An independent Quark adaptation of publicly observed patterns.
colors:
  primary: '#e60023'
  on-primary: '#ffffff'
  canvas: '#ffffff'
  foreground: '#000000'
  muted: '#62625b'
  border: '#dadad3'
rounded:
  control: 16px
  pill: 9999px
spacing:
  base: 4px
components:
  preview-button:
    height: 40px
    rounded: 16px
motion:
  source: Quark preview defaults
  strength: 0.75
  spring-stiffness: 520
  spring-damping: 21
---

# Pinterest — Quark design reference

## Scope and provenance

This is an independent Quark reference compiled from publicly visible product pages and the official sources below. It is not affiliated with or endorsed by the company. Values in this document are observations, not verified official tokens. Brand names and marks remain the property of their owners. The detail-page logo in Quark is a text identifier; the sidebar uses a separate brand symbol.

## Design principles

- **Let imagery lead** — Keep interface chrome quiet and preserve image aspect ratios.
- **Clear action hierarchy** — Use strong red for primary actions and neutral secondary controls.
- **Rounded shapes and restrained depth** — Use consistent rounded cards and inputs, with little shadow on content.

These are Quark's synthesis of the independent analysis, not a quoted official list of corporate principles.

## Color, type, layout, controls and states

- Palette: Pinterest Red `#e60023`, pressed `#cc001f`, white `#ffffff`, soft `#fbfbf9`, card `#f6f6f3`, ink `#000000`, muted `#62625b`, border `#dadad3`; focus blue `#435ee5` in the reference.
- Type: Pin Sans is proprietary; use a licensed face or an appropriate sans fallback. Reference display sizes 70/44px; headings 28/22/18px; body 16/14px; captions 12px.
- Layout: imagery retains its natural aspect ratio. Reference masonry has 8px gutters, 16px tiles and larger 32px cards; 5–6 columns on very wide displays, 4 desktop, 3 small desktop, 2 tablet, 1 on narrow mobile. Avoid treating these independent observations as official breakpoints.
- Controls: red primary, neutral secondary, transparent tertiary. Reference button 40px height / 16px radius, input 44px / 16px, search 48px / pill; filters are pills with a dark selected state. Inputs have a dark inner edge and visible blue outer focus ring.
- Depth: content cards are flat; modal layers use a scrim and shadow. Cards, search, filters, dialogs and empty states must retain their semantic and keyboard behavior.
- Writing and brand: Pinterest's official brand guidelines apply to marketing use. They say to use “save” rather than use “Pin” as a verb, and give context for calls to action and accounts. They do not constitute a complete public product UX-writing style guide. Quark examples are reconstructed.
- Official Gestalt is a Pinterest design system with its own components and guidance; this independently authored theme is not the Gestalt implementation.

## Writing experience

- **Brand names and actions** — The official brand guide says to use save rather than Pin as a verb in marketing copy. This is brand-use guidance. Example · Save an idea
- **Make the action context clear** — The official guide asks for a clear call to action and account context. Example · See more ideas on Pinterest
- **Discovery and choice** — The independent reference uses brief labels for viewing, searching and saving. Example · Search ideas / Save

- **Calls to action:** for static links to a Pinterest profile, the official brand guidelines offer phrases for following, visiting or finding ideas. They prohibit trending claims and advise using “save” instead of “Pin” as a verb.
- **Popularity claims:** the official guide limits “popular” claims to items with high impressions, clicks or saves in Pinterest Analytics and asks for the account URL to provide context. These rules apply to brand marketing, not all in-product language.

## Customization and implementation

Use semantic colors and adapt controls to the product instead of copying domain-specific copy. Expose default, hover, focus, pressed, disabled, loading and error states where applicable. Preserve label associations, keyboard navigation, readable contrast and reduced-motion preferences. The Quark adapter applies common tokens and a subset of shapes, not every reference component or official implementation. Verify font licensing and brand-asset restrictions.

## Sources

- [Pinterest · Gestalt design system](https://github.com/pinterest/gestalt)
- [Pinterest · Brand guidelines](https://business.pinterest.com/en-us/brand-guidelines/)

## Quark motion settings

These values are adjustable Quark preview defaults, not published brand motion tokens. They apply to Quark's spring-based rating and character-limit examples; other motion is governed separately by the transition duration and component-specific rules. Preserve explicit documented brand feedback, and always honor the operating system's reduced-motion preference.

- `--q-motion-strength`: `0.75` (0 disables these animations; 1 is the reference amplitude).
- `--q-spring-stiffness`: `520` (relative to the reference baseline of 520).
- `--q-spring-damping`: `21` (relative to the reference baseline of 16).
- `--q-duration`: Quark's separately editable transition duration; setting it to 0 also disables these spring animations.
