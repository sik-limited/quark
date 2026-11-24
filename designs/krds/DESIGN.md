---
version: alpha
name: KRDS
description: Korea Design System HTML Component Kit, represented as a partial light-mode theme.
colors:
  primary: '#256ef4'
  primary-hover: '#0b50d0'
  primary-pressed: '#083891'
  canvas: '#ffffff'
  foreground: '#1e2124'
  body: '#464c53'
  border: '#b1b8be'
  on-primary: '#ffffff'
typography:
  body:
    fontSize: 17px
    lineHeight: 25.5px
    fontFamily: Pretendard GOV, sans-serif
  button-large:
    fontSize: 19px
    lineHeight: 28.5px
    fontFamily: Pretendard GOV, sans-serif
rounded:
  sm: 4px
  md: 6px
  lg: 8px
spacing:
  x1: 4px
  x2: 8px
  x3: 12px
  x4: 16px
  x5: 20px
  x6: 24px
  x8: 32px
components:
  button-large:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.on-primary}"
    rounded: "{rounded.lg}"
    height: 56px
    padding: 0 20px
    typography: "{typography.button-large}"
fonts:
  roles:
    sans: Pretendard GOV
  faces:
    Pretendard GOV:
      evidence:
        - official-product-use
  safeDefaults:
    hangul:
      - Pretendard GOV
      - sans-serif
    latin:
      - Pretendard GOV
      - sans-serif
motion:
  source: Quark preview defaults
  strength: 0.4
  spring-stiffness: 520
  spring-damping: 30
---

# KRDS — 대한민국 디지털 정부 디자인 시스템

## Scope and sources

KRDS provides tokens, styles, patterns and an HTML component kit for consistent, accessible public digital services. This document records a subset of the official repository at commit `d6bb184c823e4757f05807ea4646a23e3133b6e6` (package 1.1.0). Quark's preview is an adaptation, not the complete kit or an accessibility certification. Values below resolve the repository's `rem` tokens at its 62.5% root font size, assuming a browser default of 16px; user font settings can change the actual pixel size.

## Color

Use semantic roles in the active mode. Light primary button fill is primary-50 `#256ef4`, hover primary-60 `#0b50d0`, pressed primary-70 `#083891`, and inverse static text white. Basic text is gray-90 `#1e2124`, subtle text gray-70 `#464c53`, white surface `#ffffff`, and gray border gray-30 `#b1b8be`. The kit also defines high-contrast color tokens; do not reuse light-mode hex values for every mode. Color alone should not convey a state.

## Typography

The kit includes Pretendard GOV font files. Its base line height is 1.5. PC heading sizes xlarge/large/medium/small are 40/32/24/19px; mobile sizes are 28/24/22/19px. Body large/medium/small/xsmall are 19/17/15/13px on both PC and mobile. The preview's line heights multiply these sizes by the common base line height; individual component rules may differ. The preview may use a fallback font rather than loading the official font.

## Spacing and geometry

The number tokens include 1, 2, 4, 6, 8, 10, 12, 16, 20, 24, 28, 32, 36, 40, 44, 48, 56, 64, 72, 80 and 96px. These are shared primitives, not one universal component gap. Button xsmall, small, medium, large and xlarge heights resolve to 32, 40, 48, 56 and 64px. Their radii are 4, 6, 6, 8 and 8px respectively; horizontal padding is 10, 12, 16, 20 and 24px.

## Buttons and interaction

The kit defines primary, secondary, tertiary, text and link variants with separate fill, border and text roles, including hover, pressed and disabled states. Large is the default button size, with 56px height, 8px corners, 20px horizontal padding and regular-weight label. The kit's focus outline uses a 4px primary-colored box shadow; its inset variant is 2px. Quark's static preview illustrates the primary button and approximate focus ring; it does not implement every state or high-contrast variant. Use semantic buttons, accessible names, visible focus and keyboard interaction in real products.

## Writing and assets

Use concise sentences and put the main point first. Give links understandable names; preserve visible input labels and instructions instead of relying on placeholders. Identify the field with an error and place feedback close to it. For detailed information, provide authorship, source and update information, and indicate when an action opens a new window. These practices are summarized from the official [detail content](https://www.krds.go.kr/html/site/global/global_06.html), [input form](https://www.krds.go.kr/html/site/global/global_08.html), [text input](https://www.krds.go.kr/html/site/component/component_09_03.html), and [navigation](https://www.krds.go.kr/html/site/component/component_03_02.html) guidelines; the examples on the Quark page are original. The included `logo.svg` is the official repository's `resources/img/component/icon/ico_logo_krds.svg`. Brand assets and the kit are subject to KRDS terms; consult the official copyright page before reuse. The repository README points to KRDS terms while its package metadata says ISC, so do not assume the package metadata alone resolves asset rights.

## References

- [KRDS](https://www.krds.go.kr/)
- [Official HTML Component Kit](https://github.com/KRDS-uiux/krds-uiux)
- [Token CSS](https://github.com/KRDS-uiux/krds-uiux/blob/d6bb184c823e4757f05807ea4646a23e3133b6e6/resources/css/token/krds_tokens.css)
- [Button SCSS](https://github.com/KRDS-uiux/krds-uiux/blob/d6bb184c823e4757f05807ea4646a23e3133b6e6/resources/scss/component/_button.scss)
- [Common root styles](https://github.com/KRDS-uiux/krds-uiux/blob/d6bb184c823e4757f05807ea4646a23e3133b6e6/resources/scss/common/_root.scss)

## Quark motion settings

These values are adjustable Quark preview defaults, not published brand motion tokens. They apply to Quark's spring-based rating and character-limit examples; other motion is governed separately by the transition duration and component-specific rules. Preserve explicit documented brand feedback, and always honor the operating system's reduced-motion preference.

- `--q-motion-strength`: `0.4` (0 disables these animations; 1 is the reference amplitude).
- `--q-spring-stiffness`: `520` (relative to the reference baseline of 520).
- `--q-spring-damping`: `30` (relative to the reference baseline of 16).
- `--q-duration`: Quark's separately editable transition duration; setting it to 0 also disables these spring animations.
