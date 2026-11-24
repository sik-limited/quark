---
version: alpha
name: SEED (Daangn)
description: Daangn's SEED product design system and observations of its editorial website.
colors:
  primary: '#ff6600'
  primary-pressed: '#e14d00'
  primary-weak: '#fff2ec'
  canvas: '#ffffff'
  foreground: '#1a1c20'
  body: '#555d6d'
  muted: '#868b94'
  surface: '#f3f4f5'
  border: '#dcdee3'
  on-primary: '#ffffff'
  danger: '#fa342c'
  gray-00: '#ffffff'
  gray-100: '#f7f8f9'
  gray-200: '#f3f4f5'
  gray-300: '#eeeff1'
  gray-400: '#dcdee3'
  gray-500: '#d1d3d8'
  gray-600: '#b0b3ba'
  gray-700: '#868b94'
  gray-800: '#555d6d'
  gray-900: '#2a3038'
  gray-1000: '#1a1c20'
  carrot-100: '#fff2ec'
  carrot-200: '#ffe8db'
  carrot-300: '#ffd5c0'
  carrot-400: '#ffb999'
  carrot-500: '#ff9364'
  carrot-600: '#ff6600'
  carrot-700: '#e14d00'
  carrot-800: '#b93901'
  carrot-900: '#862b00'
  carrot-1000: '#471601'
typography:
  body:
    fontSize: 16px
    lineHeight: 22px
    fontFamily: system-ui, Pretendard, sans-serif
  action-large:
    fontSize: 18px
    lineHeight: 24px
    fontWeight: 700
    fontFamily: system-ui, Pretendard, sans-serif
rounded:
  sm: 4px
  md: 8px
  lg: 12px
  xl: 16px
  full: 9999px
spacing:
  x1: 4px
  x2: 8px
  x3: 12px
  x4: 16px
  x5: 20px
  x6: 24px
  x8: 32px
  x13: 52px
components:
  action-button-large:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.on-primary}"
    rounded: "{rounded.lg}"
    height: 52px
    padding: 14px 20px
    typography: "{typography.action-large}"
fonts:
  roles:
    sans: system-ui
  faces:
    system-ui:
      evidence:
        - official-product-use
    Pretendard:
      evidence:
        - official-product-use
  safeDefaults:
    hangul:
      - Pretendard
      - Noto Sans KR
    latin:
      - Noto Sans
motion:
  source: Quark preview defaults
  strength: 0.8
  spring-stiffness: 520
  spring-damping: 20
---

# SEED — Daangn Design System

## Overview

SEED unifies design decisions behind Daangn products, from tokens and components to feedback and writing. Its stated principles are **Consistent & Essential**, **Accessible & Clear**, and **Responsive & Lively**. Product values below come from SEED documentation; the SEED website is an editorial presentation rather than a product component specification.

## Color

SEED organizes color by property (foreground, background, stroke), semantic role, variant, and state. Use semantic tokens instead of hard-coding palette colors. In light mode, `bg.brand-solid` is carrot-600 `#ff6600`, `bg.brand-solid-pressed` is carrot-700 `#e14d00`, and `fg.on-brand-solid` is white. `fg.neutral` is gray-1000 `#1a1c20`, and `fg.neutral-muted` is gray-800 `#555d6d`. The preview border uses opaque gray-400 as an approximation; `stroke.neutral-weak` is gray-alpha-400 `#000f3323`.

The palette changes in dark mode: carrot-700 becomes `#ff6600`, gray-1000 becomes `#f3f4f5`, and gray-00 becomes `#000000`. Resolve semantic roles in the active theme rather than reusing light hex values. Use brand color selectively for meaningful brand moments and core actions such as connecting neighbors; neutral solid is the usual CTA. Critical, positive, warning and informative roles communicate different states.

## Typography

Use platform system fonts on iOS, Android, and macOS; SEED recommends Pretendard on Windows, with system fallbacks. The Quark preview uses Pretendard Variable and is not a platform-faithful rendering. Font size and line height tokens use rem at a 16px base, allowing text to scale. Matching size/line pairs:

| Token | Size / line height |
| --- | --- |
| t1 | 11 / 15px |
| t2 | 12 / 16px |
| t3 | 13 / 18px |
| t4 | 14 / 19px |
| t5 | 16 / 22px |
| t6 | 18 / 24px |
| t7 | 20 / 27px |
| t8 | 22 / 30px |
| t9 | 24 / 32px |
| t10 | 26 / 35px |
| t11 | 28 / 38px |
| t12 | 32 / 42px |
| t13 | 40 / 52px |
| t14 | 48 / 60px |

Text styles combine size, line height and weight; semantic styles express roles such as screen title and article body. Let translated or enlarged text reflow and retain important information.

## Layout and spacing

SEED distinguishes content layouts from dashboard layouts. Content uses a 12-column system, ordinarily centered at larger widths, with a recommended 1040px max width or 1280px for wider browsing. Dashboard low density uses 8 columns and a 720px maximum; middle uses 12 columns and 1040px; high density is fluid with a 1040px minimum. Dashboard gutter is 24px and margin is 32px.

| Breakpoint | Viewport | Gutter | Margin |
| --- | --- | --- | --- |
| base | 0–479px | 16px | 12px |
| sm | 480–767px | 16px | 12px |
| md | 768–1279px | 32px | 24px |
| lg | 1280–1439px | 32px | 24px |
| xl | 1440px+ | 32px | 24px |

The dimension scale includes 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 24, 28, 32, 36, 40, 48, 52, 56 and 64px. Semantic spacing includes global gutter 16px, default vertical component spacing 12px, navigation to title 20px, between text 6px, and screen bottom 56px. Apply edge spacing from the safe-area boundary where present. Radius tokens are 2, 4, 6, 8, 10, 12, 14, 16, 20, 24px and full.

## Components and feedback

Action Button has XSmall, Small, Medium and Large sizes with minimum heights 32, 36, 40 and 52px. Their radii are full, 8, 8 and 12px respectively. Large with text uses 20px horizontal and 14px vertical padding, t6 bold text, and an 8px icon gap. Seven variants cover brand, neutral, critical, outline and ghost treatments. Reserve a high-emphasis CTA for the most important action; use neutral weak for supporting actions. Solid and outline combinations have different rules. Use clear action verbs, avoid unnecessary icons, and provide accessible names for icon-only controls. Support enabled, pressed, loading and disabled states.

Pressed feedback uses a distance-based calculation, not a fixed scale: `basis = max(height, width / 4, 24)` and `scale = (basis - 2) / basis`. Apply it around the center without shifting surrounding layout. Loading and disabled controls do not shrink; reduced motion disables scaling while retaining other feedback. The Quark button preview uses a fixed approximate scale and does not reproduce the complete state behavior.

## Writing and voice

Write in familiar, clear, concise words. Be friendly and lively while considering people's feelings and circumstances. Describe the user's goal or action rather than internal processing; state one idea per sentence. Prefer positive, active expressions when they clarify meaning, and make errors actionable. Avoid unnecessary honorifics, slang, abbreviations and excessive exclamation marks. Use Arabic numerals. Body sentences take a period; titles, buttons, menus and labels generally do not. Automated messages needing attention may use the more formal `~니다` tone.

For example, replace “서버 에러” with “일시적으로 서비스를 사용할 수 없어요. 잠시 후 다시 시도해주세요.” Replace “거래 후기 작성을 완료했어요” with “거래 후기를 보냈어요.” Adapt examples to the actual product context.

## SEED website and assets

The SEED homepage uses a large animated symbol on orange, oversized typography, high-contrast dark sections, generous vertical space, and illustrated foundation cards. These are observations of the design-system website, not measured product layout tokens. The locally included SEED symbol follows the official site header SVG, presented on a transparent canvas. Brand marks are separately governed; check Daangn's brand guidance before reuse. Illustrations and documentation covers remain linked through official pages.

## References

- [SEED home](https://seed-design.io/)
- [Color roles](https://seed-design.io/foundations/color/color-role) · [Palette](https://seed-design.io/foundations/color/palette)
- [Typography](https://seed-design.io/foundations/typography) · [Layout](https://seed-design.io/foundations/layout) · [Spacing](https://seed-design.io/foundations/spacing) · [Radius](https://seed-design.io/foundations/radius)
- [Action Button](https://seed-design.io/components/action-button) · [Feedback scale](https://seed-design.io/foundations/feedback/scale) · [Safe Area](https://seed-design.io/patterns/safe-area)
- [Voice and Tone](https://seed-design.io/foundations/voice-and-tone) · [Writing](https://seed-design.io/foundations/writing)
- [Documentation index for LLMs](https://seed-design.io/llms.txt)

## Quark motion settings

These values are adjustable Quark preview defaults, not published brand motion tokens. They apply to Quark's spring-based rating and character-limit examples; other motion is governed separately by the transition duration and component-specific rules. Preserve explicit documented brand feedback, and always honor the operating system's reduced-motion preference.

- `--q-motion-strength`: `0.8` (0 disables these animations; 1 is the reference amplitude).
- `--q-spring-stiffness`: `520` (relative to the reference baseline of 520).
- `--q-spring-damping`: `20` (relative to the reference baseline of 16).
- `--q-duration`: Quark's separately editable transition duration; setting it to 0 also disables these spring animations.
