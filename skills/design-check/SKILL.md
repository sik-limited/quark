---
name: design-check
description: Run one integrated design check on a page or a component by loading Quark's domain skills in order, inspecting the rendered result, and merging everything into a single ranked report. Use when asked to "check the design", "audit this page", "QA this component", "is this ready to ship", or for any design review that should cover every domain in one pass.
license: MIT
---

# Design Check

The one-shot entry point. It does not hold design rules of its own; it decides which skills apply, runs them in a fixed order against one target, and hands the evidence to `interface-review` for severity, status and the report. Think of it as the conductor: the domain skills are the players, `interface-review` is the scoring sheet.

| Skill | Role in this check |
| --- | --- |
| `interface-review` | Scope rules, always-HIGH triggers, severity, cheaper-fix ladder, report format, verdict. Load first. |
| `stress-test` | Worst-case data and state coverage. Feeds every other pass. |
| `layout` | Grouping, hierarchy, alignment, growth, responsive, RTL |
| `typography` | Scale, line-height, measure, wrapping, numerals |
| `design-tokens` | Token use across color, spacing, radius, elevation, z-index and motion; contrast; dark and high-contrast |
| `component-api` | Component checks only: whether it should exist, props/variants, state model, framework parity |
| `accessibility` | Names, focus, keyboard, targets, forms, announcements, zoom |
| `animate` | Whether things should move, easing, duration, reduced motion |
| `interface-polish` | Radius, alignment, shadows, outlines, icons, materials |
| `interface-copy` | Labels, buttons, errors, empty states, voice |
| `quark-ui` | Only when the target uses or should use Quark components and `--q-*` tokens |
| `design-to-code` | Only when a source design (image, Figma, spec) exists: compare the build against it |

## 1. Load the skills, never recall them

Before a pass, load its skill: use the installed copy if the environment has it, otherwise fetch `https://design.sik.limited/skills/<id>/SKILL.md`. Apply the rules and exact values from the loaded text, not from memory. If a skill cannot be loaded, run that domain only against the always-HIGH triggers and mark it `Partial: skill unavailable` in coverage. Load `interface-review` before anything else; its scope and severity rules govern every pass.

## 2. Resolve the target

Decide whether this is a **component** check or a **page** check, and state it. A request naming one control, widget, card or dialog is a component; a route, screen, flow or URL is a page. A page check includes the components on it, but only at page depth (section 4); a component that needs a deep look becomes a follow-up component check, not an expansion of this one.

Recon once, share with every pass: framework, styling system, token source (`--q-*` in Quark), supported viewports, theme mechanism, preview or dev-server command, project conventions (`CLAUDE.md`, `AGENTS.md`, design-system docs). Write every fix in that idiom.

If the target is too big to inspect credibly, narrow it (one flow, or the primary component set) and list what was excluded.

## 3. Component check

Order matters: later passes reuse earlier evidence.

| # | Pass | Skill | Focus at component depth |
| --- | --- | --- | --- |
| 1 | States and data | `stress-test` | Derive every state from the code branches (default, hover, focus-visible, active, disabled, loading, selected, empty, error, partial). Pick the applicable stress axes and prepare worst-case props. |
| 2 | Accessibility | `accessibility` | Accessible name, role, native element first, keyboard model (APG pattern for the widget), focus ring 3:1, target size, disabled vs `aria-disabled`, announcements. |
| 3 | Layout | `layout` | Growth (`min-width: 0`, no fixed heights), container queries over viewport, logical properties, internal grouping ratio. |
| 4 | Typography | `typography` | Role sizes and line-heights, tracking, `tabular-nums` on changing numbers, wrap and truncation behavior. |
| 5 | Tokens | `design-tokens` | Only semantic tokens, no literals (color, spacing, radius, shadow, z-index, duration); contrast of every state pair in both themes; state colors keep their meaning. |
| 6 | Motion | `animate` | Frequency gate per interaction, properties, easing, duration, interruption, exit, reduced motion. |
| 7 | Polish | `interface-polish` | Concentric radius, optical alignment, borders and shadows, icon size and stroke. |
| 8 | Copy | `interface-copy` | Label and button wording, error and empty text, placeholder use. |
| 9 | API | `component-api` | Should this be a component, variant or prop; naming matches the library; controlled and uncontrolled; same props, events and states across frameworks; used token variables declared. |

Render the component in every state from pass 1 with the worst-case data, then run passes 2-9 against those renders. If a source design exists, add a fidelity pass with `design-to-code` (Design / Built / Status) before the merge.

## 4. Page check

| # | Pass | Skill | Focus at page depth |
| --- | --- | --- | --- |
| 1 | Structure | `layout` | One primary action, importance order, grouping by space, DOM order equals visual order, hidden-content cues, responsive at every supported width. |
| 2 | System consistency | `design-tokens`, `typography` | Same role rendered the same way everywhere; one type scale; one filled action per view; off-token values (collect literals: hex, px sizes, ad-hoc shadows and radii). |
| 3 | Accessibility | `accessibility` | Tab order through the whole page, focus visible and not obscured by sticky UI, headings and landmarks, 320px reflow and 200% zoom, contrast of real content. |
| 4 | Real data | `stress-test` | The 2-4 most data-sensitive components only (lists, tables, cards, names, numbers): empty, one, many, long, RTL. |
| 5 | Motion | `animate` | Choreography of entrances, frequency of each animated interaction, nothing blocking input, reduced motion. |
| 6 | Copy | `interface-copy` | One voice and vocabulary across the page, consistent capitalization, empty and error states present. |
| 7 | Polish | `interface-polish` | Recurring detail issues, reported once against the shared source. |
| 8 | Components | all | Per component on the page: the always-HIGH triggers only. Anything deeper becomes a follow-up component check. |

## 5. Inspect the rendered result

Source alone cannot prove a visual claim. If a browser or preview tool is available, run this matrix once and share the captures with every pass:

| Condition | Component | Page |
| --- | --- | --- |
| Widths | 320px and its largest container size | 320, 768, 1280+ |
| Theme | Light and dark | Light and dark |
| Zoom | 200% | 200% |
| Preferences | `prefers-reduced-motion: reduce`, `prefers-contrast: more` | Same |
| Input | Keyboard only (Tab, Shift+Tab, Enter, Space, Esc, arrows) | Keyboard path through the primary flow |
| Direction | `dir="rtl"` if the product ships RTL | Same |

Measure, do not eyeball: computed contrast, hit-area box, computed `transition`, focus outline. Without a browser, review from source and tag every visual finding `Inferred`; mark rendering-only domains `Not verified` rather than guessing.

## 6. Running the passes

If subagents are available, run independent passes in parallel. Give each one: the loaded skill text, the resolved target, the shared recon, the capture set, and the finding schema below. Run `stress-test` first (others need its states and data) and the merge last. Otherwise run passes in the order listed.

Every pass returns findings in one schema, so the merge is mechanical:

```
severity (HIGH | MEDIUM | LOW) · domain · location (file:line or capture) · before · after · why · evidence (Observed | Inferred)
```

Each pass checks the always-HIGH triggers from `interface-review` in its domain before anything else.

## 7. Merge into one report

Hand all findings to `interface-review` section 9 rules:

- One table, ranked by severity, then reach (shared token or primitive first). No per-domain sections.
- Collapse duplicates across passes: one root cause is one row with every location. Two passes flagging the same element for different reasons stay two rows.
- When passes conflict (motion wants a transition, accessibility wants it gated), the stricter rule wins and the row cites both skills.
- Apply the cheaper-fix ladder to every `After`: delete, platform default, existing token, corrected value, then new code.
- At most 15 findings. Overflow goes to a one-line "Also noticed" list.
- Coverage table lists every domain that ran (plus API for components and fidelity when a design exists) with the evidence each pass inspected; `Clear`, `Partial`, `Not verified` or `Not reviewed` are all valid.
- End with exactly one verdict: `Block` (any HIGH remains) or `Approve` (none; name every domain not fully covered).

Open the report with a two-line summary: target and type, verdict, HIGH count, and the single most valuable fix.

## 8. Fixing (only when asked)

A check is read-only. If the user asks to fix, work the report top-down: HIGH first, shared sources before individual components, one root cause per change. After fixing, rerun only the passes whose domains the change touched, plus the trigger sweep, and update the report's statuses rather than writing a new one.

## Before you finish

| Mistake | Fix |
| --- | --- |
| Rules quoted from memory | Load the skill text; cite its values |
| Every pass on a page run at component depth | Page passes from section 4; deep component work becomes a follow-up |
| `stress-test` skipped because "the demo data looks fine" | States and worst-case data come first; other passes judge those renders |
| Visual finding backed only by source | Capture it, or tag `Inferred` |
| Separate per-domain reports | One merged table through `interface-review` |
| The same token issue reported on six components | One row on the token, six locations |
| Domain silently missing from coverage | List it as `Partial`, `Not verified` or `Not reviewed` with the reason |
| `Approve` with unreviewed domains unnamed | Name them in the verdict line |
| Fixes applied during a check nobody asked to change | Report first; fix only on request |

## Credits

Orchestrates Quark's skill pack, which is synthesized from Emil Kowalski's skills (github.com/emilkowalski/skills) and Jakub Krehel's skills (github.com/jakubkrehel/skills), both MIT.
