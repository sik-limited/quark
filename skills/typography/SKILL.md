---
name: typography
description: "Set and review how text renders: type scale, line-height, tracking, measure, wrapping, numerals, font loading and input sizing. Use when choosing font sizes or weights, fixing awkward wrapping or jittering numbers, loading fonts, or auditing text styles."
license: MIT
---

# Typography

## Use this when

Writing or reviewing any text styling: a type scale, heading levels, body copy, labels, numbers that change, truncation, punctuation, web fonts or form inputs. Write fixes in the project's own styling system and reuse its tokens.

Scope limits: the words themselves belong to `interface-copy`, heading semantics and zoom/reflow to `accessibility`, RTL layout to `layout`, whether a color pair passes to `design-tokens`.

## Measured vs heuristic

Treat these as findings when missed: unitless line-height, weight 400+ below 18px, 60-75ch measure, 16px inputs on iOS, `tabular-nums` on changing values. Treat tracking, pairing and scale ratios as heuristics: report them only where they break the project's own scale. Never propose a new typeface unless a type change was asked for.

## 1. Fonts: few, loaded, honest

- At most 3 families (marketing may use more). Hierarchy comes from size and weight; every extra face dilutes it. Pair for contrast (serif headline over sans body), never two near-identical sans faces.
- Default to `system-ui, sans-serif` unless a brand face is required. A custom face needs a fallback stack.
- Ship `.woff2` only (`.woff` solely for ancient browsers; never raw `.ttf`/`.otf`). Use `font-display: swap`, preload only the one or two faces used above the fold, and subset when possible.
- Load every weight and style the design uses. A missing face is faked by the browser (synthetic bold/italic) and looks smeared. Do not set `font-synthesis: none` on body text; if a verified isolated treatment needs it, scope it and prefer the longhand (`font-synthesis-weight`, `-style`, `-small-caps`, `-position`).
- Variable font vs static: one or two weights, static files can be smaller; several weights, optical sizes or axes, variable wins.
- "Display" in a name is not a display font. If a family ships `Display` and `Text` cuts, use the one matching the size.
- Use CSS properties, not raw tags, so fallbacks keep working:

| Want | Write | Not |
| --- | --- | --- |
| Weight | `font-weight: 650` | `font-variation-settings: "wght" 650` |
| Tabular digits | `font-variant-numeric: tabular-nums` | `font-feature-settings: "tnum"` |
| Slashed zero | `font-variant-numeric: slashed-zero` | `"zero"` |
| Optical size | leave `font-optical-sizing: auto` | hard-coded `"opsz"` |
| Custom axis / `ss01` | raw tag is fine here | - |

## 2. Role scale

Define a few sizes and pair each with line-height and weight, so a role is one decision. Name by role on teams (`--text-body`), not `sm/lg`.

| Role | Size | Line-height | Weight | Tracking |
| --- | --- | --- | --- | --- |
| Display | 36px / 2.25rem | 1.1 | 600 | -0.02em |
| Title | 24px / 1.5rem | 1.2 | 600 | -0.01em |
| Heading | 18px / 1.125rem | 1.3 | 600 | 0 |
| Body | 16px / 1rem | 1.5 (1.6 long-form) | 400 | 0 |
| Caption | 13px / 0.8125rem | 1.4 | 400 | 0 |
| Overline (uppercase) | 12-13px | 1.3 | 500-600 | +0.05em |

```css
:root {
  --text-caption: 0.8125rem;
  --text-body: 1rem;
  --text-heading: 1.125rem;
  --text-title: 1.5rem;
  --text-display: 2.25rem;
}
h1 { font-size: var(--text-display); line-height: 1.1; letter-spacing: -0.02em; }
h2 { font-size: var(--text-title);   line-height: 1.2; letter-spacing: -0.01em; }
h3 { font-size: var(--text-heading); line-height: 1.3; }
```

- Emphasis inside a role is one weight step (400 to 500), not a size change.
- Heading levels descend through the scale; deep levels may share a size if weight or tracking keeps them distinct. A heading never drops below body size unless it is a deliberate overline.
- Use `rem` for `font-size` so the user's browser setting applies; `em` for tracking; unitless line-height always.
- Quark mapping: `--q-font-size` (13px default) is the UI/caption base for dense controls. Derive other roles from it or from `rem`, and extend the project's tokens rather than adding ad hoc sizes.

## 3. Size and weight floors

| Context | Floor |
| --- | --- |
| Long-form body | 16px (move off only for a nameable reason: small-running face, dense tool) |
| Inputs, menus | 14px desktop, 16px on mobile (see section 8) |
| Captions | 13px; rarely under 12px |
| Weight under 18px | 400 or heavier |
| Weight 100-300 | 28px and up, and checked against its background even there |

## 4. Line-height

Display 1.1, headings 1.2-1.3, body 1.5-1.6. Anything that can wrap to 3+ lines is at least 1.4, even in a height-constrained card: a card description at `line-height: 1.1` is a bug. Larger text gets tighter leading, smaller text looser; scripts with tall ascenders/descenders need more.

## 5. Tracking and kerning

- Headings at 24px and up: -0.01em to -0.02em. One fixed value for all sizes is wrong somewhere.
- Uppercase labels at 14px or less: +0.05em.
- Body: 0. Always `em`, never `px`.
- Kerning is on by default; `font-kerning: none` is never a fix.

## 6. Measure and wrapping

- Cap long-form text at 60-75 characters: `max-width: 65ch`, or about 560-680px at 16px. Recheck the cap when body size changes.
- `text-wrap: balance` on headings and short descriptions only. Never on paragraphs; Chromium ignores it past 6 lines.
- `text-wrap: pretty` on paragraphs to avoid a lone last-line word. Safe on long-form.
- `overflow-wrap: break-word` wherever a long word, URL or ID can escape (`anywhere` inside flex children that must shrink).
- `white-space: nowrap` for labels and badges where a break looks broken.
- Interface text stays `text-align: start`. `justify` only in deliberate editorial layouts.
- Hyphenation: `hyphens: auto` with a correct `lang`, or `&shy;` at chosen break points.

```css
.truncate { overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.clamp-3 {
  display: -webkit-box; -webkit-box-orient: vertical;
  -webkit-line-clamp: 3; overflow: hidden;
}
```

Every truncated value needs a way back to the full text (tooltip, expand, detail view); see `layout`.

## 7. Numbers

`font-variant-numeric: tabular-nums` on timers, counters, prices, table columns, progress percentages, anything that updates in place, so digits keep one width and nothing shifts. Leave figures in prose proportional. Use `slashed-zero` for IDs and code where `0`/`O` confusion matters.

## 8. Inputs on mobile

iOS Safari zooms the page when an input's text is under 16px. Two fixes, same result, different look; ask which the design wants:

1. Size up on mobile: `font-size: 16px` below the breakpoint, design size above it.
2. Keep `font-size: 16px` and render smaller via `transform: scale(ratio)` on the input inside a clipping wrapper that draws the field surface and focus outline, with width divided by the ratio and line-height divided by the same factor. Identical at all viewports, more code.

Never disable zoom with `maximum-scale=1` (see `accessibility`). Style `::placeholder` and `caret-color` for the hint and cursor; a custom caret is rarely worth it.

## 9. Characters and details

Store text in natural case; apply `text-transform` in CSS so a redesign never rewrites copy. Render typographic characters, never keyboard stand-ins (code keeps straight quotes):

| Instead of | Use |
| --- | --- |
| `"quoted"` / `'` | curly quotes and apostrophe |
| `2010-2020` | en dash `2010–2020` |
| ` -- ` aside | em dash `—` |
| `...` | single ellipsis `…` |
| `10 km`, `Fig. 3` | non-breaking space (`&nbsp;`) so the pair never splits |
| unwanted breaks in names/units | `white-space: nowrap` on that span |

- Underlines: `text-underline-position: from-font; text-decoration-thickness: from-font;` or tune `text-underline-offset` (about 3px) and 1px thickness. Keep `text-decoration-skip-ink` at default. Dotted underline on `abbr`/defined terms hints extra info. Only the underline color animates reliably.
- Mixed direction: set `lang` and `dir`; wrap names, numbers or URLs inside opposite-direction text in `<bdi>`. Digit runs keep their order in RTL; do not reorder by hand. Paragraphs of 3+ lines align to their own script.
- `text-box: trim-both cap alphabetic` removes the extra space above and below letters so text centers optically in buttons and badges. Needs both edges (`trim-start cap text` for top only). Chromium 133+, Safari 18.2+; progressive enhancement.
- Set `-webkit-font-smoothing: antialiased` and `-moz-osx-font-smoothing: grayscale` once on the root, never per component.
- Decorative text (drop caps, gradient text, outlines, shadows) stays real text: `::first-letter`, `initial-letter`, `background-clip: text`, `-webkit-text-stroke`, `text-shadow`. If a stroke shows inner lines, the variable font has overlapping contours: use a static cut.
- Keep text selectable. `::selection` may carry brand if the selected pair stays legible. `user-select: none` only on drag/gesture surfaces, never a layout root.

## 10. Korean (Hangul)

Latin defaults in sections 2-9 do not transfer unchanged. Every number below is a starting point: tune it per font on real copy, in a browser.

**Language and wrapping**

- Set `lang="ko"` on `<html>` (or the element) and `lang="en"` on embedded English passages (proper names and single technical terms do not need it). `lang` drives font fallback and glyph variants for shared Han characters, line-breaking rules and screen-reader voice. Select Korean-only rules with `:lang(ko)`.
- Hangul is spaced by eojeol (word units). Default browser wrapping breaks between syllables, which splits words mid-way. Use `word-break: keep-all` for prose, headings, labels and buttons so breaks happen only at spaces. Never `word-break: break-all` on prose; reserve it for a deliberate narrow cell such as a fixed-width ID.
- `keep-all` makes a long eojeol, URL or token unbreakable, so pair it with `overflow-wrap: anywhere` (inside flex or grid children that must shrink) or `break-word`. Skip `word-break: break-word`; it is deprecated.
- `line-break: strict` mostly affects punctuation and small kana rules. Leave it at `auto` unless a style guide demands it; `keep-all` is the lever that matters for Hangul.
- `hyphens: auto` does nothing useful for Hangul; do not rely on it.
- `text-wrap: pretty` is fine on Korean paragraphs. `text-wrap: balance` works on headings and short descriptions, but with `keep-all` there are fewer break points, so lines come out uneven or hit the container edge sooner. Check the narrowest width and keep `overflow-wrap` as the safety net.

```css
:lang(ko) {
  word-break: keep-all;
  overflow-wrap: break-word; /* use anywhere only on flex/grid children that must shrink: it also lowers min-content width */
  font-synthesis-style: none; /* for Hangul: the scoped longhand exception from section 1; no faux italic */
}
:lang(ko) :is(h1, h2, h3, .title) { text-wrap: balance; }
:lang(ko) p { text-wrap: pretty; }
```

**Vertical rhythm and tracking**

- Hangul glyphs fill most of the em box, so at the same px size they look larger and denser than Latin and need more room. Body line-height about 1.6-1.75, headings about 1.3-1.4. Section 2's 1.1 display leading is for Latin; raise it to at least 1.25 for Hangul.
- For Hangul, section 5's values are relaxed. Tracking is a common practice, not a rule: with typical Korean UI fonts such as Pretendard, body text is often set slightly negative (about -0.01 to -0.02em) and large headings a bit tighter (about -0.02 to -0.03em). Fonts differ, so compare against 0 and keep the setting only if it clearly reads better.
- Hangul has no case. Do not apply uppercase tracking (+0.05em) or `text-transform: uppercase` to Korean; it does nothing for Hangul and silently uppercases any Latin inside. Never encode meaning in case; use weight, size or color.
- No italics for Hangul: most Korean fonts ship none, and a synthetic oblique looks broken. Emphasize with weight (one step, 400 to 500/600) or color. Load real weights; faux bold smears (see section 1).

**Font stacks**

- Simplest: one Korean webfont that includes Latin (for example Pretendard), then system fallbacks.
- Pairing: put the Latin brand font first and the Korean font after it. The browser picks glyphs per character, so Latin comes from the first font and Hangul falls through to the next. Order decides which Latin glyphs you get; it does not change Hangul unless the first font also contains Hangul.
- Always end with platform Korean fonts and a generic: `"Apple SD Gothic Neo", "Malgun Gothic", sans-serif` (Android and Linux reach their Noto CJK face through `sans-serif`; `"Noto Sans KR"` is the Google Fonts name and only helps if loaded or installed).
- Latin and Hangul fonts differ in size, baseline and line metrics, which shows as uneven lines in mixed text and a layout jump when the webfont swaps in. Match them with `size-adjust`, `ascent-override`, `descent-override` and `line-gap-override` on a local-fallback `@font-face`, and tune the values against the final font.

```css
@font-face {
  font-family: "Ko Fallback";
  src: local("Apple SD Gothic Neo Regular"), local("AppleSDGothicNeo-Regular"), local("Malgun Gothic"); /* local() takes a full or PostScript name, not a family */
  size-adjust: 98%;          /* tune per font pair */
  ascent-override: 92%;
  descent-override: 24%;
  line-gap-override: 0%;
}
body { font-family: "Pretendard", "Ko Fallback", "Noto Sans KR", sans-serif; }
```

**File size**

- A full Korean font covers all 11,172 precomposed modern syllables (U+AC00-D7A3) and is far larger than a Latin face. Use `.woff2`, `font-display: swap`, and preload only the critical weight.
- Subset: a KS X 1001 subset (2,350 common syllables) is small but lacks rarer syllables, which then fall back to another font. Safer is `unicode-range` slicing (or a dynamic-subset build of the font) so the browser downloads only the slices a page uses and every syllable stays available.
- Ship fewer weights (often 400, 500/600, 700) before shrinking glyph coverage.

**Numbers, units, punctuation**

- `tabular-nums` still applies to Korean UIs (prices, counters, timers). Keep a number and its unit or counter together: attached forms such as `3개` never split, and a spaced pair uses `&nbsp;`.
- Use standard punctuation: `.` and `,` followed by a space, curly quotes `“ ”` (and `‘ ’`), and the single ellipsis `…`. Follow the product's copy style for brackets and quote marks, and stay consistent. See `interface-copy` for the wording itself.

**Sizes**

- Small Hangul loses legibility sooner than Latin because of its dense strokes. Avoid under 12px; use 13px or more for captions, and keep body at 16px with the 1.6-1.75 leading above. Quark's default 13px `--q-font-size` is a dense-control base, not a target for reading text.

## Before you finish

| Pattern | Fix |
| --- | --- |
| `font-weight` with no loaded face for it | Load the face or use a shipped weight |
| `font-synthesis: none` on body | Remove; scope a single longhand if needed |
| `font-variation-settings: "wght"/"opsz"` | `font-weight`; drop `opsz` |
| `font-feature-settings: "tnum"/"zero"` | `font-variant-numeric` |
| `line-height` in `px`/`rem` | Unitless |
| `line-height` under 1.4 on text that wraps | 1.4+ |
| `letter-spacing` in `px` | `em` |
| `font-size` in `px` for body | `rem` |
| Weight 100-300 under 28px | 400+ |
| `text-wrap: balance` on a paragraph | `pretty` |
| `text-align: justify` in app UI | `start` |
| `-webkit-line-clamp` without `display: -webkit-box` + orient | Add both |
| Timer, counter, price, numeric column without `tabular-nums` | Add it |
| `...`, `--`, hyphenated ranges, straight quotes in rendered copy | Typographic characters |
| `text-decoration-skip-ink: none` | Remove; tune offset or `from-font` |
| Input under 16px with no mobile override | Section 8 fix |
| Font smoothing set inside a component | Move to root |
| Name, number or URL in opposite-direction text without `<bdi>` | Wrap in `<bdi>` |
| `user-select: none` on a text container | Remove |
| Long paragraph with no max-width | Cap at 60-75ch |
| More than 3 families | Consolidate |
| Korean text with `break-all`, or no `keep-all` | `word-break: keep-all` plus `overflow-wrap: break-word` (`anywhere` in shrinking flex/grid children) |
| Korean page without `lang="ko"` | Set `lang` on root; `lang="en"` on English runs |
| Hangul with `italic`, uppercase or +0.05em tracking | Weight or color; no case or label tracking |
| Hangul body line-height under 1.5 or headings under 1.25 | 1.6-1.75 body, 1.3-1.4 headings |
| Hangul under 12px (captions under 13px) | 13px+ |
| Full Korean font, no subset or `unicode-range` | Slice, `.woff2`, `swap`, preload one weight |
| Latin and Korean fonts jump or misalign on swap | `size-adjust` and metric overrides on fallback |
| `.ttf`/`.otf` served to browsers | Convert to `.woff2` |

Severity: HIGH when text is unreadable, clipped at 320px width or 200% zoom, or truncated with no way back. MEDIUM when it breaks the type system or heading hierarchy. LOW for isolated polish. Report what you could not verify in a browser as not verified.

## Credits

Synthesized for Quark from Emil Kowalski's skills (github.com/emilkowalski/skills) and Jakub Krehel's skills (github.com/jakubkrehel/skills), both MIT.
