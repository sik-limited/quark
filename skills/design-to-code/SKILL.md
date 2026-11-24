---
name: design-to-code
description: Turn a screenshot, image, Figma frame or written spec into code that reuses the project's existing components and tokens, then verify and report how closely it matches. Use when asked to implement, build or recreate a design, mockup or reference image.
license: MIT
---

# Design to Code

## Use this when

You are given a design (screenshot, exported image, Figma frame or link, or a written spec) and asked to build it. The design decides what to build; the project decides how: its components, its tokens, its conventions. A design value is a request, not permission to invent a token, component or arbitrary number.

Where the design breaks a domain rule, build it as designed and report the conflict with the owning skill: type `typography`, spacing and structure `layout`, tokens and contrast `design-tokens`, focus and semantics `accessibility`, copy `interface-copy`, surfaces and icons `interface-polish`, motion `animate`. Stress cases belong to `stress-test`, new component APIs to `component-api`, the full review to `design-check`.

## 1. Read the input and rate its reliability

| Input | What you get | Reliability |
| --- | --- | --- |
| Figma frame with variables, styles, auto layout | Exact values, token names, structure intent | Measured |
| Figma frame with raw values only | Exact numbers, no token intent | Measured, needs mapping |
| Exported image or screenshot | Ratios and colors you read by eye | Estimated |
| Written spec | Intent and constraints, few numbers | Assumed |

- Tag every value you collect as measured or estimated, and carry the tag into the report.
- Ask for the Figma link when an image of the same design exists; it replaces the whole estimate.
- Figma: read variables and styles as the token intent, auto layout gap and padding as the spacing intent, and component instances as the component to use. Absolute x/y on a free layer is where someone dropped it, not a rule. Save assets into the project; never leave a design-tool URL in code. Use whichever Figma access the session offers; if none exists, ask the user to connect one or export the frame, and do not build from memory.
- Images: find the scale before measuring anything.
  1. Detect 1x, 2x or 3x from an element whose real size you know: a 16px or 24px icon, a 44px control, a system status bar, a 1px hairline.
  2. If nothing is known, assume 2x for a retina capture or 1x for a desktop frame, state it, and expect every value to be off by that factor if wrong.
  3. Divide measured pixels by the scale. A screenshot shrunk to fit also shifts every number; ask for the original capture size.
- Scope the run to the frames named. Frames showing the same thing at different widths are one piece at several breakpoints, not separate pieces.

Done when you hold, for each frame, its dimensions, the scale, and tagged values for spacing, size, radius, color, type and the components used.

## 2. Inventory before writing code

Write a short tree, top to bottom: regions (header, sidebar, content, footer), then the components inside each, then the states each component shows.

| Region | Element | Component candidate | States shown | States missing |
| --- | --- | --- | --- | --- |
| Header | Search field | Input | default | focus, error, disabled |
| Content | Plan card | Card | default, selected | loading, long title |

Read the whole frame first. A repeated structure is one component rendered many times; do not build ten cards.

## 3. Map every element to an existing component first

1. Check the project's own components, then Quark's catalog: `https://design.sik.limited/registry.json` and `https://design.sik.limited/llms.txt`, with the detail page at `https://design.sik.limited/en/components/{id}/` for variants and behavior. Use `quark-ui` for adaptation details.
2. Use the existing component with the variant the design shows. Compose from existing parts before extending them.
3. Build a new component only when nothing fits and the pattern repeats or carries behavior. Then follow `component-api` for its props, slots and states.
4. A one-off layout group is plain markup in place, not a new component file.
5. A design element with no project equivalent: build the closest plain markup, list it as Deviation, and ask whether a component should exist.

## 4. Map every value to a token

Read the project's tokens first (in Quark: `--q-text`, `--q-muted`, `--q-accent`, `--q-on-accent`, `--q-surface`, `--q-border`, `--q-border-width`, `--q-radius`, `--q-space`, `--q-gap`, `--q-control-px`, `--q-control-py`, `--q-panel-padding`, `--q-shadow`, `--q-duration`, `--q-font-size`). Then fill one row per distinct value:

| Design value | Source | Token | Nearest token | Action |
| --- | --- | --- | --- | --- |
| 8px gap | measured | `--q-gap` | - | Use |
| 15px title | estimated | none | 16px body | Snap, note |
| #344E41 fill | measured | `--q-accent` | - | Use |
| 22px radius | measured | none | `--q-radius` | Ask |

Snap rule:

- Exact match, or a bound variable with the same name: use the token.
- Within 2px, or within one step of the scale, or the same color family one step away: snap to the token and record it as Snapped.
- Further than that, or a color on a different step: stop and ask. Say what the design shows, the nearest token, and the visible effect of rounding.
- Never invent a token, a variant or an arbitrary value (`w-[343px]`, a raw hex, a one-off `px`) to hit a number. If the design truly needs a new token, say so and extend the project's tokens with the owner's approval (see `design-tokens`).
- Values estimated from an image snap more readily than measured ones, since the number itself is uncertain; still note it.

## 5. Build only what the design shows

- Implement the frames, the breakpoints they define, and the states they draw (hover, empty, error). Nothing else.
- A state the design does not draw stays at the existing component's default and is listed as Missing. If it affects layout or data (loading, error, empty, long text), ask, or apply the defaults from `stress-test` and flag them as Assumed.
- Leave everything around the piece untouched: no neighboring copy edits, no icon swaps, no drive-by refactors or renames. A diff wider than the design is the most common failure.
- Keep semantics even when the input is a picture: a button is a `button`, a list is a list, headings descend. Visual match never overrides `accessibility`.
- Data the design implies but the project does not have: build the UI fed by props, mark it Unwired, and do not invent endpoints.

## 6. Responsive behavior the design does not state

Do not guess silently. Apply these inferences and state each as an assumption:

| Situation | Default inference |
| --- | --- |
| One desktop frame only | Content keeps its width cap; multi-column grids collapse to one column when they no longer fit; padding steps down one level; nothing scrolls horizontally at 320px |
| One mobile frame only | Keep the single column and let it grow to the content cap; center it; do not stretch lines past 75ch |
| Frames at several widths | Interpolate between them with fluid sizing; add a breakpoint only where the layout truly changes |
| Fixed-width element in the frame | Treat as a max width, not a fixed width |
| Hover drawn, or no touch frame | Gate hover behind `(hover: hover) and (pointer: fine)` |

Ask when two reasonable readings change structure (a sidebar becoming a drawer versus a stack).

## 7. Text and assets

- Use the design's real copy. Use placeholder text only where the design itself does, and keep its length. Do not rewrite copy; send wording issues to `interface-copy`.
- Korean and English differ in length and density. If the design shows one language, check the other at the same widths and report any overflow. For Hangul rules see `typography` section 10.
- Icons: use the project's icon set first, matching name, size and stroke. Do not hand-trace an icon from a screenshot or paste an SVG when an equivalent exists. If none exists, list it as Missing with the name and size needed.
- Photos and illustrations: export from the design file when available. With an image-only input, use a placeholder of the right aspect ratio, never a crop of the design screenshot itself.
- Fonts: use the design's face only if the project already loads it; otherwise map to the project's stack and note it.

## 8. Verify against the design

1. Render the build at each frame's width and put it beside the design (side by side, or an overlay at 50% opacity).
2. Walk it element by element on the properties you collected: size, spacing, radius, color, type, alignment, order. "Gap is 12px, design is 16px" is a finding; "feels tight" is not.
3. Compare the states the design shows, in the same state, with the same content.
4. Fix every mismatch you caused, then compare again. What remains should be only snaps and open questions.
5. No browser available: give the local URL with the widths to check, and say the comparison is not verified. Never report a match you did not look at.

## 9. Report and hand off

| Element | Design | Built | Status |
| --- | --- | --- | --- |
| Card padding | 20px (measured) | `--q-panel-padding` | Exact |
| Title size | 15px (estimated) | 16px token | Snapped |
| Mobile nav | not shown | Collapses to one column | Assumed |
| Status badge | filled pill | text label | Deviation: no badge component |
| Error state | not drawn | component default | Missing |

Status values: Exact (equals the design through a token), Snapped (within the snap rule, noted), Assumed (design silent, inference stated), Deviation (differs and needs the user's call), Missing (not in the design or not buildable yet).

Then list in one line each, omitting empty lists: Unwired data, Missing states, Domain conflicts with the owning skill, questions waiting on the user. Close with where it runs and at which widths you compared.

Finish by running `design-check` on the built page or component for the full integrated review.

## Before you finish

| Pattern | Fix |
| --- | --- |
| Values read from an image with no scale stated | State the scale, tag as estimated |
| Raw hex, `px` or arbitrary utility that matches a token | Use the token |
| New token, variant or component added to hit a number | Remove; snap or ask |
| Snap beyond 2px or one step with no question asked | Ask before building |
| New component file for a one-off group | Inline as markup |
| Hand-built control that an existing component covers | Use the component |
| Loading, error or empty branch the design never drew | Remove it; list as Missing |
| Files in the diff that no frame touches | Revert them |
| Hand-traced icon or design-tool URL in code | Use the icon set or exported asset |
| Hard-coded sample data not mentioned in the report | List as Unwired |
| Responsive behavior chosen silently | State the assumption in the report |
| "Matches" claimed with no render of the build | Render each width or say not verified |
| Contrast or focus change the design does not show | Restore the design and report the conflict |
| No hand-off after finishing | Run `design-check` |

## Credits

Synthesized for Quark from Jakub Krehel's skills (github.com/jakubkrehel/skills), MIT.
