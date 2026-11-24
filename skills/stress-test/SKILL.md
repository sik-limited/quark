---
name: stress-test
description: Stress-test a component or screen with worst-case content, quantities, container widths, states, and environments, then report each break with its CSS/JS fix. Use when asked to stress-test, break, find edge cases, "try the worst case", or when a component renders data it does not author.
license: MIT
---

# Stress Test

Demo data is chosen, usually unconsciously, to make a design look good: names that fit on one line, counts that need no separator, every optional field filled. Undo that choice one field at a time. Change the data, not the component: feed the worst case through the same boundary the demo data uses (fixture, mock, props, API stub). Never hand-edit markup or CSS to produce a break.

## Use this when

- A component renders text, numbers, images, or lists it does not author (user input, CMS, API, translations).
- A component repeats over items, has loading/empty/error states, or lives in a container whose width it does not control.
- Before shipping a card, list row, table, form, badge, avatar, nav item, or toast.

For layout rules see `layout`; wrapping and numerals see `typography`; empty/error copy see `interface-copy`; focus and zoom see `accessibility`; dark-mode failures see `design-tokens`.

## 1. Map what the component renders

List every value it shows, with source, type, limit, and whether it is optional. Include values people forget: counts in headers, relative times, badge text, button labels from data, tooltips, avatars, and the list length itself.

| Field | Source | Limit | Optional |
| --- | --- | --- | --- |
| `name` | `member.name` | 255 (schema) | no |
| `email` | `member.email` | none found | no |
| `role` | `member.title` | 120 | yes |

Look up limits in validation schemas, DB columns, API types, and `maxLength` attributes. No limit found is itself a finding. Where frontend and backend disagree, assume the larger one will arrive through an import or the API.

**Rule: plausible or schema-backed, never random.** `"aaaa…"` ×5,000 proves nothing and gets dismissed. Use realistic shapes or the true limit. Use `example.com`, `example.org`, or `.test` for emails and URLs.

## 2. Pick the applicable axes

An axis earns a slot only when its cue matches the component. Keep the matching axes, drop the rest, and say which you dropped and why in one line so a wrong inference is cheap to catch.

| Axis | Cue (keep when...) | Scenarios |
| --- | --- | --- |
| Content length | It renders text it does not author | empty, one word, typical, several sentences, one unbreakable string (URL, email, German compound) |
| Content shape | Text may come from users/locales you do not write in | emoji (alone and mixed), RTL, mixed direction, diacritics and tall scripts, CJK without spaces, columns of numbers |
| Quantity | It repeats over items | 0, 1, typical, 10x typical, huge (1,000+ if unpaginated), exactly page size and page size + 1 |
| Container | Always | 320px, squeezed by a flex/grid sibling, narrow sidebar (~280px), very wide (2560px) |
| State | The component has the state (props or data branches) | loading, empty, error, disabled, partial data, every enum value at once, no permission |
| Environment | The project supports the mode | dark mode, 200% zoom / large text, reduced motion, `dir="rtl"`, touch (hover-only actions) |

A text input gets length and states, never quantity. A static icon button with a fixed label gets container and environment only.

### Derive states from code branches

States are the branches the code already has. Read the component and its data hooks:

- Data: loading, empty, error, one item, typical set, a set long enough to scroll/paginate, some requests resolved while others pend, stale data refetching.
- Account: role/permission checks, owner vs member, plan tier.
- Feature: flags, trials, limits reached, locked or disabled features.

A state the code cannot reach is not a state; do not add a branch to render it. If the design shows a state the code lacks, report it as missing. Combine kinds only where the code branches on the combination (`member-empty`), never as a cross product. Hold loading states still instead of resolving on a timer.

## 3. Build the worst-case dataset

Put one worst-case fixture beside the demo data, shaped identically. Mix failures across rows as real data does (row 1 long name, row 2 long email, row 3 one-letter name) because the first rows on screen matter most. Add separate fixtures for empty, one, and huge.

### Condensed catalog

| Kind | Values | Breaks |
| --- | --- | --- |
| Names | `Aleksandra Wiśniewska-Kowalczyk`; `Christopher Alexander Montgomery III`; `Jo`; `J`; `Đặng Thị Ngọc Hân`; `王秀英`; `نور الهدى عبد الرحمن`; `🦊 Fox`; `👩🏽‍💻 Priya`; `  Sam   Lee `; `dana`; missing name | wrapping at hyphens, wrong initials (`CI`, `�`), clipped diacritics, RTL punctuation, ZWJ emoji slicing, empty words, name-less fallback |
| Emails / URLs / ids | `bartholomew.fitzgerald@northwind-industries-holdings.example.com`; `a@b.test`; `first.last+billing@example.com`; a 100-char URL with query string; a UUID; `Q3 Board Deck — FINAL (revised) v12 [approved by legal].pdf`; `IMG_20250914_183022_HDR_edited_edited.HEIC` | no break opportunity, siblings pushed off-screen, end-truncation hides the part that differs |
| Labels / copy | `Senior Product Design Engineer, Platform Infrastructure`; `Invitation expired 12 days ago`; `Benachrichtigungseinstellungen`; French/German translation of a short label (+30% length); 12 tags on one item; one 45-char tag; `Untitled`/`""`/`"   "`; `<script>` / `&amp;` / `**bold**`; a value with a newline; a 2,000-char description | badge wrap, tag wall, collapsed headings, escaping layer, row height doubling |
| Numbers | `0`, `1`, `1284`, `1000000`, `12345678.9` as currency, `-42.5`, `0.1+0.2`, `142%`, `-3%`, `null`/`NaN`, `1.284` in `de-DE`, a live value going `99` to `100` | "1 members", missing separators, raw floats, progress bars past bounds, locale separators, width jitter |
| Collections | 0, 1, page size, page size + 1, 1,000+, one item 10x taller, identical names | empty state, "1 of 1", off-by-one pagination, perf, grid stretch |
| Time | now, 12 days / 11 months / 3 years ago, future date, `1970-01-01`, `2025-12-31T23:30:00-08:00`, a 1,284-hour duration | "0 seconds ago", `-3 days ago`, timezone day shift, durations that never roll up |
| Media | avatar URL that 404s, no avatar, 4000x200 and 200x4000 images, transparent or dark logo in dark mode, slow image | broken-image icon, distortion, layout shift, invisible logos |

## 4. Break it

Check the worst case at the component's real container width, then 320px, then the widest layout it supports. Then 200% browser zoom (text grows, boxes do not), dark mode, `dir="rtl"` on a wrapper, and a touch device if hover reveals actions. If no browser is available, reason from the CSS and say which findings were verified and which inferred. Never report a predicted failure as observed.

### Failure signature to fix

| What you see | Cause | Fix |
| --- | --- | --- |
| Avatar/icon squished to an oval | Flex child shrinking | `flex-shrink: 0` on avatar, icon, any fixed-size box |
| Text overflows instead of wrapping | Flex/grid child `min-width: auto` | `min-width: 0` on the text column; `minmax(0, 1fr)` in grid |
| Email/URL runs past the edge | No break opportunities | `overflow-wrap: anywhere` on that element |
| Trailing action (menu, button) pushed off or clipped | Middle content took the space | `min-width: 0` on middle, `flex-shrink: 0` on the action |
| Badge wraps to two lines | Badge allowed to shrink | `white-space: nowrap; flex-shrink: 0`; decide what yields instead |
| Avatar adrift beside a three-line name | `align-items: center` on rows of varying height | `align-items: flex-start` once text can wrap |
| Last row cut mid-glyph | Fixed-height box, no affordance | Visible scrollbar or fade mask; confirm overflow is intended |
| Heading breaks mid-word | `word-break: break-all` | `overflow-wrap: anywhere` (breaks only when forced) |
| Wrong initials | `name.split(' ')[0][0]` | `Intl.Segmenter` graphemes, first + last word, skip suffixes (Jr, Sr, II, III), icon fallback |
| Orphan `—` or empty line for missing field | Placeholder always rendered | Omit the line, or reserve its height on purpose |
| "1 members", "0 member" | Hardcoded plural | `Intl.PluralRules` or per-count strings |
| Numbers jitter, columns misalign | Proportional figures | `font-variant-numeric: tabular-nums` |
| `1284`, `NaN`, `undefined`, float noise | Raw number rendered | `Intl.NumberFormat` with user locale; guard null |
| Translated label overflows button | Fixed `width` | Content width with `min-width`; never fixed `width` |
| Vietnamese/Thai diacritics clipped | Tight `line-height` + `overflow: hidden` | Looser line-height or no clipping on text boxes |
| Broken-image icon | No error fallback | `onerror` swaps to initials; `object-fit: cover` for any aspect ratio |
| Ellipsis with no way to read it | `text-overflow` and nothing else | Keyboard- and touch-reachable tooltip, expand control, or detail view (a hover-only `title` does not count) |
| 1,000 rows stutter | Every row rendered | Virtualize or paginate; say which |
| Raw `<b>`, `&amp;`, `**x**` shown | Wrong escaping layer | Escape once at render; never inject user data as HTML |
| Skeleton shifts when content arrives | Skeleton does not match layout | Reserve sizes with fixed dimensions or `aspect-ratio` |
| Hover-only actions unreachable | Revealed on `:hover` only | Visible on touch; gate hover with `@media (hover: hover) and (pointer: fine)` |
| Chevrons/icons on wrong side in RTL | Physical properties | Logical properties (`margin-inline-start`); mirror directional icons |

Quark-flavored baseline for a row: `display: flex; gap: var(--q-gap); padding: var(--q-panel-padding);` with the text column `min-width: 0` and the action `flex-shrink: 0`. Extend the project's tokens rather than adding one-off values.

```css
.row { display: flex; align-items: flex-start; gap: var(--q-gap); }
.row__avatar, .row__action { flex-shrink: 0; }
.row__text { min-width: 0; }
.row__email { overflow-wrap: anywhere; }
.row__badge { white-space: nowrap; flex-shrink: 0; }
.num { font-variant-numeric: tabular-nums; }
```

```js
const initials = (name = "") => {
  const seg = new Intl.Segmenter(undefined, { granularity: "grapheme" });
  const words = name.trim().split(/\s+/).filter(w => !/^(jr|sr|ii|iii|iv)\.?$/i.test(w));
  const g = w => [...seg.segment(w)][0]?.segment ?? "";
  return (words.length > 1 ? g(words[0]) + g(words.at(-1)) : g(words[0] ?? "")).toLocaleUpperCase();
};
const plural = new Intl.PluralRules(navigator.language);  // pick the string by plural.select(n)
const fmt = new Intl.NumberFormat(navigator.language);    // fmt.format(1284)
```

## 5. Truncate, wrap, or clamp

Decide per field, not globally.

| Strategy | Use for | How |
| --- | --- | --- |
| Wrap | Text needed in full to identify something: names, titles in detail views | Default flow; two lines fine, four means the column is too narrow |
| End-truncate | Secondary metadata where the start carries meaning: role, description, preview | `overflow: hidden; text-overflow: ellipsis; white-space: nowrap` on a `min-width: 0` box, plus full-text access |
| Middle-truncate | Items that differ at the end: file names, paths, hashes, emails on one domain | Split into head and tail spans; tail `flex-shrink: 0`, head ellipsizes |
| Clamp | Multi-line previews in cards | `display: -webkit-box; -webkit-box-orient: vertical; -webkit-line-clamp: 2; overflow: hidden` |
| Never truncate | Numbers, amounts, dates, anything compared | Give them room; use compact notation (`1M`) only where precision does not matter |

Every ellipsis needs full-text access (tooltip, detail view, or expandable; not a bare `title`) that also works from the keyboard and touch.

## 6. Report

Fix only when asked, except trivial fixes the user requested up front. Some breaks are decisions (truncate or wrap? show "—" or hide?): list them, recommend one, and stop. After fixing, flip through every state again, including demo data, because a fix for the worst case must not regress the normal case. Keep the worst-case fixture as a regression test.

Severity: **Broken** (unreadable content, unreachable action, wrong data), **Ugly** (readable but visibly wrong), **Fragile** (fine now, one realistic step from breaking: no limit, no fallback).

| # | Severity | Field | Worst-case value | What happens | Fix (file:line) |
| --- | --- | --- | --- | --- | --- |
| 1 | Broken | `email` | `bartholomew.fitzgerald@…example.com` | Pushes the menu off the row at 400px | `min-width: 0` on text column, `overflow-wrap: anywhere` |

Then: decisions for the user (one line each with a recommendation), what held up (so the test is credible and the user knows what not to touch), and axes dropped. "Everything survived" is a complete report; do not pad it with preferences. State plainly which findings were observed and which inferred.

## Before you finish

| Mistake | Fix |
| --- | --- |
| Random or absurd data (`aaaa…` x5,000) | Use realistic patterns or the schema limit |
| Only long text tested | Also try 1-letter names, missing fields, count of 1, empty list, translated labels |
| Every axis run on every component | Keep matching axes and name the dropped ones |
| Worst case produced by editing markup/CSS | Feed it through the data boundary |
| Fix breaks the demo data | Re-check demo, worst case, empty, one, and huge |
| Truncation with no full-text access | Add a keyboard-reachable tooltip, expand control or detail view |
| States invented that code cannot reach | Report them as missing instead |
| Findings phrased as taste | Report what visibly broke, with value and cause |

## Credits

Synthesized for Quark from Emil Kowalski's skills (github.com/emilkowalski/skills) and Jakub Krehel's skills (github.com/jakubkrehel/skills), both MIT.
