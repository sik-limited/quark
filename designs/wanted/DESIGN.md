---
version: alpha
name: Wanted (Montage)
description: A Quark interpretation of Wanted's Montage design system, drawing on official guidance and labeled observations.
colors:
  primary: '#0066ff'
  on-primary: '#ffffff'
  canvas: '#ffffff'
  foreground: '#171719'
  body: '#565860'
  border: '#dedfe2'
  error: '#ff4242'
  positive: '#00bf40'
  caution: '#ff9200'
  gradient-start: '#0066ff'
  gradient-middle: '#ff53c0'
  gradient-end: '#ff5e00'
typography:
  body:
    fontSize: 16px
    lineHeight: 24px
    fontWeight: 500
    fontFamily: Pretendard JP, Pretendard Variable, sans-serif
  body-reading:
    fontSize: 16px
    lineHeight: 26px
    fontWeight: 500
    fontFamily: Pretendard JP, Pretendard Variable, sans-serif
  heading:
    fontSize: 22px
    lineHeight: 30px
    fontWeight: 700
    fontFamily: Pretendard JP, Pretendard Variable, sans-serif
rounded:
  preview-button: 12px
  preview-card: 12px
  preview-chip: 9999px
spacing:
  base: 4px
  gutter: 20px
  medium: 16px
  large: 24px
components:
  preview-primary-button:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.on-primary}"
    rounded: "{rounded.preview-button}"
    height: 48px
fonts:
  roles:
    sans: Pretendard JP
  faces:
    Pretendard JP:
      evidence:
        - official-product-use
    Wanted Sans:
      evidence:
        - official-product-use
  safeDefaults:
    hangul:
      - Pretendard Variable
      - Noto Sans KR
    latin:
      - Noto Sans
motion:
  source: Quark preview defaults
  strength: 0.7
  spring-stiffness: 520
  spring-damping: 22
---

# Wanted — Montage design reference

## Scope and evidence

[Montage](https://montage.wanted.co.kr/docs/getting-started) is Wanted's design system. This document draws on official Montage public documentation. Quark's styling is an adaptation, not the official library. Several spacing, breakpoint, elevation and semantic names in this reconstruction differ from official public tokens. Radius scales and most component pixel measurements below are observations, not asserted official Montage tokens. Validate current source before using exact values.

## Design principles

1. **Extensibility** — structure components so they can evolve and adapt to more than one context. Prefer composable leading and trailing slots, clear size variants and explicit states; preserve semantics when customizing.
2. **Consistency** — use shared semantic roles and component behavior to make actions, feedback and hierarchy predictable across screens and platforms.
3. **Efficiency** — reuse established decisions and components to produce consistent quality with less repeated work. Customize supported slots and tokens instead of forking every component.

The three principle names and high-level intent are from Montage. The implementation advice above is Quark's interpretation.

## Color and hierarchy

Use semantic foreground, surface, background and line roles rather than assigning raw palette colors by sight. Montage separates neutral, brand, positive, cautionary, negative, disabled and accent roles, as well as focus lines and elevated surfaces. Dark mode has its own semantic values; do not invert the light palette mechanically.

The independent reference identifies Wanted Blue `#0066ff`, white surfaces and a cool neutral hierarchy. It reports transparent foreground and line colors; the six-color Quark preview uses opaque approximations for secondary text and border because its basic token model accepts six-digit hex. The reference distinguishes branded artwork and interface chrome: blue provides functional emphasis; default cards use quiet hairlines, with stronger elevation on overlays. Its simplified blue → magenta → orange gradient is an observation, not a reusable official logo specification. The official logo has more complex gradient construction and is a separately governed asset.

## Typography

The official typography guide specifies Pretendard JP to support Korean, English and Japanese. Wanted Sans is also an open-source Wanted typeface; do not imply it is the default Montage product font. Quark currently previews with Pretendard Variable as a fallback. The official 19-style scale is:

| Style | Size / line height |
| --- | --- |
| Display 1 / 2 / 3 | 56 / 72 · 40 / 52 · 36 / 48px |
| Title 1 / 2 / 3 | 32 / 44 · 28 / 38 · 24 / 32px |
| Heading 1 / 2 | 22 / 30 · 20 / 28px |
| Headline 1 | 18 / 26px |
| Headline 2 | 17px; official table lists 26px and 1.412 together, which conflict. Quark uses 24px from 1.412. |
| Body 1 normal / reading | 16 / 24 · 16 / 26px |
| Body 2 normal / reading | 15 / 22 · 15 / 24px |
| Label 1 normal / reading | 14 / 20 · 14 / 22px |
| Label 2 / Caption 1 / Caption 2 | 13 / 18 · 12 / 16 · 11 / 14px |

Heading tracking is generally negative; smaller labels use positive tracking in the official scale. Keep text responsive and test line wrapping with translated and enlarged content.

## Layout and spacing

The official breakpoint tokens are min-width xs `0`, sm `768`, md `992`, lg `1200`, xl `1600` px. The published guide gives max content widths including padding of 1100px at lg and 1440px at xl. It illustrates two mobile, three tablet and twelve desktop columns with a 20px gutter. Spacing is generally a multiple of 4px, with 2px or occasional 1px optical adjustments. The independent reference also describes a separate marketplace reconstruction with different breakpoints: treat those as surface observations, not as Montage tokens.

## Components and states

- **Buttons:** official variants are Solid and Outlined, with Primary, Assistive and Negative roles. Use Negative for destructive actions; width is customizable, while each component size has a fixed height. The reference's reconstructed large 48px / 12px example and 28, 32, 40, 48, 56px size sequence are observed handoff values, not numeric measurements claimed by the official design page.
- **Inputs:** keep a visible label and helper or error message associated with the field. The independent example uses a 48px field and a blue focus glow. Provide disabled, error, focus and keyboard states.
- **Autocomplete:** combines a text field with suggested choices. Montage documents normal, search, avatar, checkbox and thumbnail menu variants; the Quark addition is a native single-value suggestion field, not all Montage variants.
- **List cell:** compose leading content, a title, optional description and trailing content; choose padding, alignment and divider to fit the context. Quark's existing list-row is an adaptation, not an exact copy of every variant.
- **Feedback:** maintain distinct loading, error, empty and success states; use icon and text along with color. The independent reference places status messages near the affected content and uses elevated overlays for transient feedback.
- **Chips:** the independent example uses a dark selected pill and a blue-tinted brand variant. Expose selection with semantic state, not color alone.

## Surface, icon and motion

The official elevation documentation describes layered shadows. The independent reference observes 1px card borders, small shadows for switches and selected segments, and stronger depth on floating menus and dialogs; its exact shadow values are not official published tokens. Montage's icon gallery distinguishes Solid, Color and Nav categories; monochrome icons commonly inherit `currentColor`. Do not treat all official icons as monochrome.

The reference describes short transitions and a focus ring, but says duration/easing values are not official motion tokens. Quark's focus and interaction settings are examples. Preserve visible keyboard focus and respect reduced-motion settings.

## Content and writing

The reference does not establish an official UX writing guide. It observes concise action labels, confirmation messages that report a result, and empty states that suggest an available next step. Use clear field labels and actionable error text. Quark's examples are reconstructed. Recruitment-specific labels, rewards and hiring flows belong to that domain and should not be reused as generic design rules.

## Implementation and asset boundaries

The official system's terms identify MIT licensing for the software, including attribution and license-text requirements. Brand marks have separate guidelines. The logo shown in Quark identifies the source and is not a general license to reuse it. The theme adapter applies common tokens, type and some control geometry; it does not implement every official Montage component, state or dark-mode mapping. Verify accessibility and the active theme's contrast in the destination product.

## References

- [Montage · Getting started and principles](https://montage.wanted.co.kr/docs/getting-started)
- [Semantic colors](https://montage.wanted.co.kr/docs/foundations/base-material/colors/semantic) · [Typography](https://montage.wanted.co.kr/docs/foundations/base-material/typography) · [Grid](https://montage.wanted.co.kr/docs/foundations/base-material/grid)
- [Button](https://montage.wanted.co.kr/docs/components/actions/button/design) · [Autocomplete](https://montage.wanted.co.kr/docs/components/presentation/autocomplete/design) · [List cell](https://montage.wanted.co.kr/docs/components/contents/list-cell/design)
- [Montage terms](https://montage.wanted.co.kr/docs/getting-started/terms-of-use) · [Montage web repository](https://github.com/wanteddev/montage-web)

## Quark motion settings

These values are adjustable Quark preview defaults, not published brand motion tokens. They apply to Quark's spring-based rating and character-limit examples; other motion is governed separately by the transition duration and component-specific rules. Preserve explicit documented brand feedback, and always honor the operating system's reduced-motion preference.

- `--q-motion-strength`: `0.7` (0 disables these animations; 1 is the reference amplitude).
- `--q-spring-stiffness`: `520` (relative to the reference baseline of 520).
- `--q-spring-damping`: `22` (relative to the reference baseline of 16).
- `--q-duration`: Quark's separately editable transition duration; setting it to 0 also disables these spring animations.
