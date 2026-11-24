---
version: alpha
name: RIDI
description: An independent Quark interpretation of Ridi's reading and content discovery interfaces; not an official Ridi design system release.
colors:
  primary: '#1e9eff'
  on-primary: '#ffffff'
  canvas: '#ffffff'
  foreground: '#141414'
  body: '#666666'
  muted: '#787878'
  border: '#e6e6e6'
  surface: '#f5f5f5'
  blue-50: '#e9f5ff'
  blue-500: '#1f9eff'
  blue-600: '#1c90e8'
  blue-660: '#0077d9'
  rating: '#dc3232'
  navy-badge: '#324155'
  update: '#1ecdcd'
  divider: '#f2f4f5'
typography:
  body:
    fontSize: 15px
    lineHeight: 22px
    fontFamily: Pretendard Variable, sans-serif
  heading:
    fontSize: 22px
    lineHeight: 26px
    fontWeight: 700
    fontFamily: Pretendard Variable, sans-serif
rounded:
  sm: 2px
  cover: 4px
  purchase: 6px
  search: 8px
  pill: 99px
spacing:
  xs: 4px
  sm: 8px
  md: 12px
  lg: 16px
  xl: 24px
  container: 30px
components:
  cover:
    rounded: "{rounded.cover}"
  purchase:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.on-primary}"
    rounded: "{rounded.purchase}"
fonts:
  roles:
    sans: Pretendard Variable
  faces:
    Pretendard Variable:
      evidence:
        - live-surface-use
  safeDefaults:
    hangul:
      - Noto Sans KR
    latin:
      - Noto Sans
motion:
  source: Quark preview defaults
  strength: 0.5
  spring-stiffness: 520
  spring-damping: 28
---

# RIDI — Content discovery and reading

## Scope

This is an independent Quark interpretation of Ridi's public screens, not an official Ridi release. It distinguishes published code values, observed rendering and estimated dimensions. The examples below use fictional content and placeholders. Ridi's marks remain its own brand assets.

## Visual system

White surfaces and restrained gray text make room for artwork. The observed emotion blue `#1e9eff` identifies current navigation, selected genre pills and purchase actions; the separately published raw blue-500 is `#1f9eff`. Rating and discount details use red `#dc3232`, while free and update badges may use navy and mint. Covers and cards use 4px corners and a subtle inset outline, with badges attached to the cover edge. The reconstruction reports white on blue at roughly 2.8:1; use an accessible foreground/background combination in production.

## Typography and layout

Pretendard Variable is the referenced sans family. Representative text styles include 16/24, 15/22, 14/22, 13/20 and 12/18px. The reference describes a 1170px maximum container with 30px side padding, reduced to 16px at 767px and below. Compact shelves become horizontally scrollable on mobile. The spacing scale is 2, 4, 6, 8, 10, 12, 16, 20, 24, 28, 30, 36 and 40px; published radius values are 2px, 4px and full. Other shapes are component-specific: purchase buttons 6px and genre pills 99px.

## Components

- **Cover card:** artwork with a small corner badge; title, author and rating sit below. Keep artwork proportions stable and support missing images and long titles.
- **Ranked content row:** rank, compact cover, title, supporting information and rating; preserve reading order when it wraps.
- **Episode row:** thumbnail, title, release metadata and an explicit read action. Keep selection and navigation as separate controls when both exist.
- **Genre pills:** selected state uses a blue fill; expose selection semantically and do not rely on color alone.
- **Purchase button:** blue fill and 6px radius; the reference estimates 52px height rather than specifying it. Quark's focus and press states are examples, not verified Ridi behavior.

## References

- [Ridi](https://ridibooks.com/) — product site.

## Quark motion settings

These values are adjustable Quark preview defaults, not published brand motion tokens. They apply to Quark's spring-based rating and character-limit examples; other motion is governed separately by the transition duration and component-specific rules. Preserve explicit documented brand feedback, and always honor the operating system's reduced-motion preference.

- `--q-motion-strength`: `0.5` (0 disables these animations; 1 is the reference amplitude).
- `--q-spring-stiffness`: `520` (relative to the reference baseline of 520).
- `--q-spring-damping`: `28` (relative to the reference baseline of 16).
- `--q-duration`: Quark's separately editable transition duration; setting it to 0 also disables these spring animations.
