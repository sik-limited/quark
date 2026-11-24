---
name: component-api
description: "Design and review component APIs for a cross-framework library: whether to build at all, naming, state model, composition, React/Vue/vanilla parity, token-based styling, spec writing and deprecation. Use when adding a component, adding a prop or variant, splitting or merging components, writing a component spec, or reviewing an API for consistency."
license: MIT
---

# Component API

Use this when you add a component, add or rename a prop, argue over variant vs new component, write a spec, or review whether an API is consistent. A Quark component ships four sources of one behavior: `preview.svelte` (the rendered preview and the source of the Svelte example), `react.jsx`, `vue.vue` and `vanilla.js`. Button is the only one shipping a full prop API in all four (plus a reusable `component.svelte`); Switch, Input, Tabs and Dialog are prop-less demos with internal state, and Tabs states its prop contract in `spec/`. The API is the part that must not drift between sources.

Scope limits: ARIA roles, keyboard maps and focus rules belong to `accessibility`; tokens, color and contrast to `design-tokens`; spacing and sizing to `layout`; motion to `animate`; visible labels and errors to `interface-copy`; component quality sweeps to `interface-review`; turning a design into code to `design-to-code`.

## 1. Decide whether to build

Run the tests in order. Stop at the first match.

| Test | Result |
|---|---|
| A native element does the job (`<dialog>`, `<details>`, `<input type=range>`, `<select>`, `popover`) with acceptable styling | Do not build. Style the native element, ship a recipe in `style.css` |
| A project component or installed library already meets the contract | Reuse it. Map the contract onto its API, do not wrap it in a second one |
| Different role, different keyboard model or different state machine (Tabs vs Accordion, Switch vs Checkbox) | New component |
| Same structure and behavior, different emphasis or size (fill vs weak, small vs large) | Variant or size value on the existing component |
| Same behavior, one caller needs an extra icon, badge or layout | Caller composition through children, slots or snippets. No prop |
| Two or more callers repeat the same assembly of existing parts | Compose a new component from them (a preset), keep the parts public |
| One caller, one screen, no reuse in sight | Leave it in the caller. Extract at the second real use |

Anti-patterns to name in review:
- **Boolean explosion.** `primary`, `secondary`, `danger`, `ghost`, `small`, `large` as separate booleans permit nonsense combinations. Use one enum per axis (`color`, `variant`, `size`; see Button).
- **God component.** Over about 12 props, or props that only matter when another prop has a specific value, means two components or a compound component.
- **One-off variant.** A value added for a single screen that no other caller can name. Move it to the caller (`className`, `htmlStyle`, a `--q-*` override).
- **Look-named API.** `BlueCard`, `rounded`, `bigText`. Name by role and meaning.
- **Re-implemented native.** A custom checkbox that drops form participation, `name`, `required` or autofill.
- **Config object for markup.** `items=[{...}]` that grows a field per use case. Offer children or slots for the content, keep the list for identity and label only.

## 2. Naming

### Components
- Name by role, not look: Tabs, Switch, Dialog, Pagination, Progress, Skeleton, Tooltip. Not SegmentedBar or PillToggle. A look word is acceptable only when it is the product's own proper name.
- Folder id is the lowercase kebab URL id (`bottom-sheet`, `list-row`, `text-area`) and must equal `component.json` `id`. Display names are bilingual and carry no framework prefix.
- The root class is `q-<name>` plus state hooks. Never namespace by framework.

### Props: one vocabulary across the library
Reuse the word Quark already uses before inventing a synonym.

| Concept | Use | Not |
|---|---|---|
| Emphasis | `variant` (Button: `fill`, `weak`) | `type`, `kind`, `style` |
| Semantic color family | `color` (`primary`, `danger`, `light`, `dark`) | `intent`, `theme`, `scheme` |
| Size | `size` (`small`, `medium`, `large`, `xlarge`) | `big`, `sm` mixed with `small` |
| Width behavior | `display` (`inline`, `block`, `full`) | `fullWidth`, `block` booleans |
| Root element swap | `as` (`button`, `a`) | `tag`, `component`, `element` |
| Accessible name of a group | `label` (the tablist `label`) | `title`, `ariaLabel` on the prop surface |
| Selection | `value` + `defaultValue` | `selected`, `current`, `active` |
| Unavailable | `disabled` | `inactive`, `enabled={false}` |
| Busy | `loading` | `pending`, `busy` |
| Native passthrough | `type`, `href`, `name`, `value`, `target`, `rel` unchanged | Renamed versions |

- Boolean props are positive adjectives that default to `false`: `disabled`, `loading`, `checked`, `open`, `readonly`. Never a negative (`noBorder`, `hideIcon`) and never a boolean that must default to `true`.
- Two or more booleans that cannot be true together are an enum. Three booleans for two mutually exclusive states is a bug.
- Enum values are lowercase words, no `-ish` suffixes, ordered weakest to strongest. The default is the value used most, written in the spec.
- Identity values (tab value, item id) are stable strings, never array indexes. Selection by index breaks when the list changes.

### Events
Same meaning, native idiom in each framework. Name the event for what the user did to the component's state, not for the DOM event that caused it.

| Meaning | Svelte | React | Vue | Vanilla |
|---|---|---|---|---|
| Native activation | `onclick` | `onClick` | `emit('click')` | `onClick` option |
| Value changed by the user | value callback prop (Tabs guidance: `onValueChange`), or a `$bindable()` prop for `bind:value` | `onChange` (native-shaped) or `onValueChange(value)` | `update:modelValue` | `onChange` option, or `change` CustomEvent with `detail.value` |
| Open state | `open = $bindable(false)` prop, used as `bind:open` | `open` + `onOpenChange(open)` | `v-model:open` | `open()` / `close()` plus `close` event |

- Fire once per user-caused change, never on mount, never when the new value equals the current one, never for a change the caller made through props.
- Pass the value, not the event, when the component owns the value. Pass the native event when the component is a thin wrapper over a native control.
- Do not invent a second name for something the platform names (`click`, `input`, `change`, `close`, `toggle`).

## 3. State model

### State matrix
Decide every cell before writing markup, then check each in the preview.

| Group | States |
|---|---|
| Interaction | rest, hover, `:focus-visible`, active/pressed, selected/checked/expanded |
| Availability | enabled, disabled, read-only, loading/busy |
| Data | empty, one item, many, overflow (long text, too many items), error, partial/indeterminate |
| Validation | untouched, valid, invalid with message, invalid after submit |
| Environment | RTL, narrow container, forced-colors, reduced motion, touch (no hover) |

A state that exists in the matrix but not in the preview does not exist yet. See `stress-test` for the failure catalog.

### Rules
- **One source of truth.** The selected value lives in one place. The DOM attribute (`aria-selected`, `aria-checked`, `hidden`) is rendered from it, never read back as state.
- **Derive, do not duplicate.** Active index from selected value; `hasError` from `error`; count from items. Storing a second copy needs a sync effect, and sync effects drift.
- **Controlled and uncontrolled.** For any component with a user-changeable value, support both: `value` (controlled), `defaultValue` (uncontrolled start), and a change notification. Controlled means the caller owns the value and the component does not mutate it; an invalid controlled value displays a safe fallback (the first item for Tabs) without writing back. Decide controlled vs uncontrolled once at mount from whether `value` is `undefined`; warn in development if it flips.
- Purely presentational components (Badge, Skeleton, Progress) take values and hold no state.
- Open/close and transient states (`pressed`, `focus`) are internal unless the caller needs to drive them. When they do, expose `open` + `onOpenChange` rather than imperative-only methods; vanilla may add `open()` and `close()` too.
- Keep applied values and their previews when editing is disabled or a panel closes. Mount the editor across availability changes, or store the value in its parent if it needs to unmount. Check that re-enabling and reopening restore the previous value and editor settings.
- A component never mutates caller data and never reads global state. Everything arrives through props, children or context.
- Disabled vs `aria-disabled`: use native `disabled` for genuinely unavailable controls; use `aria-disabled` plus handler blocking only when the control must stay focusable (reason shown, pending submit). Button's loading state is the second case: it blocks activation in the handler and sets `aria-busy`. The decision table is in `accessibility`.

## 4. Composition

- **Children, slots, snippets for content.** Props for identity, configuration and state. Anything a caller may want to fill with markup (label, icon, panel content, footer) is a slot: `children` in React, default and named slots in Vue, snippets in Svelte, `children` option (string or Node) in vanilla.
- **Compound components for multi-part widgets.** When parts have their own props or order (Tabs / TabList / Tab / TabPanel, Accordion / Item / Trigger / Panel, List / Row / Footer), expose the parts. Share state through context (React), `provide/inject` (Vue), `setContext` (Svelte); vanilla returns a root with child factories or a `value` setter. Keep the list-of-items shorthand only for the common case and implement it on top of the parts.
- **Forward everything native.** The root accepts and spreads unknown props and attributes onto the real root element: `className`/`class`, `style`, `id`, `data-*`, `aria-*`, handlers, `ref`. Spread first, then set the component's own attributes after so callers cannot break required ARIA. Merge `className` and `style`, never replace them. Vue sets `inheritAttrs: false` and applies `v-bind="$attrs"` to the real root (see Button). Vanilla accepts a small explicit option list (`label`, `title`, `name`, `value`, `target`, `rel`) and `htmlStyle`/`style`, since it has no spread.
- **Do not swallow native props.** A text field must still accept `name`, `autocomplete`, `inputmode`, `required`, `maxlength`, `placeholder`. If you wrap a native control, pass them through, do not whitelist a subset.
- **Refs.** Expose the root (and the focusable element when different) so callers can focus, measure and attach observers: `ref` forwarding in React, `defineExpose({ focus })` in Vue, `bind:this` in Svelte, the returned element in vanilla.
- **`as` for root swap** only between elements with the same semantics (button/a for actions). Never `as="div"` on an interactive component. When swapped to `a`, handle missing `href` and disabled the way Button does (`role="link"`, `tabIndex`, `aria-disabled`).
- **Stable IDs.** Generate with the framework's SSR-safe id (React `useId`, Vue `useId`, Svelte `$props.id()` as in the Tabs preview, a module counter in vanilla). Never `Math.random()`. Instances must be independent.

## 5. Cross-framework parity

The behavior is identical in all four sources. Only the idiom changes. If a behavior cannot be reproduced in one framework, the contract is wrong; fix the contract, not one framework.

| Concern | React | Vue | Svelte (preview) | Vanilla |
|---|---|---|---|---|
| Props | function params, destructured with defaults | `defineProps` with defaults | `let { ... } = $props()` with defaults (Button `component.svelte`) | options object with defaults |
| Controlled value | `value` + `onValueChange` | `modelValue` + `update:modelValue` | `value` + `onValueChange` (Tabs guidance); `$bindable()` if `bind:value` is wanted | `value` option + `onValueChange`, `setValue()` |
| Uncontrolled start | `defaultValue` | `defaultValue` | `defaultValue` | `defaultValue` |
| Events | `onXxx` callbacks | `defineEmits`, kebab or camel per project | callback props (`onclick`) | `onXxx` option; CustomEvent when the component is a long-lived element |
| Content | `children`, render props sparingly | slots | snippets (`children: Snippet`, `{@render children?.()}`) | `children`: string or Node |
| Attributes | `...rest` spread | `$attrs` | `...rest` | explicit option list + `htmlStyle` |
| Boolean attrs | `disabled`, `data-disabled={x \|\| undefined}` | same via `:` binding | same | `dataset` and attribute helpers |
| State hooks | `data-*` on root | `data-*` on root | `data-*` on root | `dataset` on root |
| Cleanup | effect return | `onBeforeUnmount` | `$effect` or `onMount` return, `onDestroy` (Dialog, Button previews) | `destroy()` / `AbortController` |
| Return | JSX element | component | component | DOM element (see below) |

- **Vanilla convention.** The module default export is a factory returning a DOM element, ready to append (`createButton(options = {})`; the demos take no options, e.g. `createSegmentedTabs()`). Later changes go through methods on the returned element (`element.setState({ loading, disabled })` on Button) or a documented `setValue` / `destroy`. Current demos have no `destroy`; for new long-lived components register listeners with one `AbortController` and abort it in `destroy` (Tabs guidance: `initTabs` returning `setValue` and `destroy`). Build markup with `template.innerHTML` for static structure and `textContent` or `append` for caller content, never `innerHTML` on untrusted text.
- Same defaults, same enum values, same DOM output (roles, `aria-*`, `data-*` attributes, class names) in every source. Diff the DOM each of the four sources renders; a mismatch is a bug.
- Same event semantics: fire conditions, payload shape, order relative to the DOM update.
- The CSS is shared. Never fork selectors per framework or style by framework-specific classes. SSR-safe: no `window` or `document` at module top level in React and Vue files.
- Naming asymmetry is allowed only for idiom (`onClick` vs `click`, `class` vs `className`, `modelValue` vs `value`). Everything else, including prop names and enum values, matches.
- Where Quark sources currently differ (tabs guidance names `onValueChange` and Vue `modelValue`; the Switch, Input and Tabs demos keep internal state, Tabs selects by index, and Input's React demo uses the native input `onChange`), follow the component's own spec; for a new component with a user-changed value, use `value` / `defaultValue` plus a value-passing callback and say so in the spec.

## 6. Styling API

- Style only through semantic tokens: `--q-text`, `--q-muted`, `--q-accent`, `--q-on-accent`, `--q-surface`, `--q-border`, `--q-border-width`, `--q-radius`, `--q-space`, `--q-gap`, `--q-control-px`, `--q-control-py`, `--q-panel-padding`, `--q-shadow`, `--q-duration`, `--q-font-size`. Allowed names are the keys of `component-variables.ts`; `text`, `muted` and `font-size` are always included.
- List exactly the tokens the CSS reads in `component.json` `variables`, without the `--q-` prefix (Tabs: `accent`, `surface`, `border`, `radius`, `space`, `gap`, `control-py`, `shadow`, `duration`). Add every token you start using, remove those you stop using. If a listed token derives from `--q-space` (`gap`, `control-px`, `control-py`, `panel-padding`), `space` is added automatically.
- No hard-coded colors, radii, spacing or durations in the CSS. Derive from tokens (`calc(var(--q-space) * 3)`, `color-mix(in oklab, var(--q-accent) 12%, var(--q-surface))`). A component-local variable (`--q-button-background-color`) is acceptable as an override hook with a token-based default. Existing literals (Button's danger `#f04452`) are legacy, not precedent.
- **State and variant hooks are data attributes on the root**: `data-variant`, `data-color`, `data-size`, `data-display`, `data-disabled`, and `data-state="press"` / `"focus"` (Button's forced-state hooks, matched alongside `:active` / `:focus-visible`). Style with `[data-variant='weak']`, not extra classes. This keeps the four implementations and the preview "force state" tooling aligned.
- Style ARIA state where it exists (`[aria-selected='true']`, `[aria-checked='true']`, `[aria-expanded='true']`) rather than mirroring it with a class.
- Gate hover with `@media (hover: hover) and (pointer: fine)`, keep `:focus-visible` always visible, never `transition: all`. Transition `--q-duration` so the preview's duration token works. Motion details live in `animate`.
- Per-component CSS lives in the folder's optional `style.css`, with selectors scoped under `.q-preview` (Tabs, Button). It is loaded into the gallery and playground preview automatically; it reaches copied specs only through the spec's `css` field. Token derivation and contrast checks belong to `design-tokens`.

## 7. Accessibility contract per component

Every component states three things before it is built, in the spec:
1. **Role and structure**: native element first, otherwise the role and required parts (tablist, tab, tabpanel).
2. **Accessible name**: where it comes from (visible text, `label` prop, `aria-label` required for icon-only).
3. **Keyboard model**: which keys do what, whether activation is automatic, focus movement and roving `tabindex`.

Do not restate patterns here. Look up the matching APG pattern in `accessibility` and cite it. Props that exist only to satisfy accessibility are required, not optional: an icon-only button without a name, a tablist without `label`, an input without an associated label must fail loudly in development (a warning) or by type.

## 8. Writing the spec

A component folder works with only `component.json`, `preview.svelte`, `react.jsx`, `vue.vue` and `vanilla.js`: the detail page shows the preview, variables and copyable source. Add `spec/index.ts` when it earns the cost.

| Add a full spec when | Examples suffice when |
|---|---|
| Keyboard model, focus management or roving tabindex (Tabs, Menu, Slider) | Presentational, no keyboard of its own (Badge, Skeleton, Avatar) |
| Controlled and uncontrolled value, or open state | Props map one-to-one onto a native element |
| Multi-part structure or ID wiring (aria-controls, labelledby) | A single visual with a few enums |
| Behavior AI or a team will reimplement in a different stack | The shipped source is the spec (`includeSource: true`) |

`spec/index.ts` default-exports a `ComponentSpecification`. Fill each field with a purpose:

| Field | Content |
|---|---|
| `behavior` (en/ko) | Two sentences: what the user can do and what persists (keys, selection, hidden panels staying mounted) |
| `apiDescription` | Whether this is a contract for generated code or the shipped API |
| `apiRows` | One row per public prop or event: `name`, `type` plus default, one-line `description`, all bilingual. Short rows; edge cases go in `api.md` |
| `guidanceIntro` | Inspect the project, reuse an existing component or library that meets the contract, then apply the framework guidance |
| `copyDescription`, `promptIntro` | What the copy button bundles; the one-sentence prompt (Button, with `includeSource`, leaves it empty) |
| `spec` (markdown string: Tabs imports `spec.md?raw`, Button inlines it) | Common contract: choose the implementation, structure and accessibility, interaction, explicit non-goals, verification |
| `api` (markdown string, e.g. `api.md?raw`) | Table `Feature / Type / Default / Description`, then edge-case rules (empty list, invalid controlled value, when change fires) |
| `css` | The folder `style.css` imported with `?raw`, never a copy |
| `guidance` | `Record<Framework, string>`: `react`, `svelte`, `vue`, `vanilla` markdown (Tabs imports `frameworks/*.md?raw`), each: reuse first, then suggested props, state and ID approach, cleanup |
| `includeSource` (optional) | `true` when the shipped Svelte/React/Vue/vanilla source is the reference (Button) |

Writing rules for specs:
- Imperative, testable sentences. "Exactly one tab is selected", not "tabs should generally have a selection".
- State non-goals explicitly (Tabs: no vertical orientation, no manual activation, no RTL keys). Unspecified is not the same as unsupported; say which.
- Every `apiRows`, `behavior` and `description` field has both `en` and `ko`, written in natural language rather than translated word for word. Keep identifiers, props and key names in code font, untranslated.
- Verification is a checklist the implementer can run: clicks, keys, one tabbable tab, valid ARIA associations, unique IDs, independent instances, empty and changing lists, controlled and uncontrolled, no accidental form submission.
- Spec text is theme-independent data. Never embed a color, radius or duration value in it; point to tokens.
- After adding a spec, run the folder check (`node tests/component-folder.integration.mjs`), `npm run check` and `npm run build`.

## 9. Evolution

- **Additive by default.** New optional prop with a default that keeps today's behavior. New enum value appended. New slot or part. All safe.
- **Never silently change a default**, an enum meaning, the DOM structure callers select against, an event's fire condition or a token's role. These are breaking; treat them as a rename plus removal.
- **Rename or remove path**, one release cycle each:
  1. Add the new name. Keep the old as an alias that maps onto it; the new one wins if both are set.
  2. Warn once per component in development when the old name is used (`console.warn`, named replacement).
  3. Document the pair in the spec: old, new, removal release. Update examples to the new name.
  4. Remove after the cycle, and update all four sources and the preview in the same change.
- Removing a token from `variables` is breaking for consumers who override it; deprecate the same way.
- Changing behavior for a bug fix (a change firing on mount) is allowed, but state it in the change note.
- Versioning: a breaking API change bumps the major version of any published package. Quark's detail pages show current source, so keep spec, `component.json` and the four sources in the same commit.

## Before you finish

| Check | Fix |
|---|---|
| Component could be a native element, a variant or caller composition | Take the cheaper option from section 1 |
| Several booleans for one axis, or a prop used only with another prop's value | Replace with one enum, or split the component |
| Name describes appearance (`rounded`, `blue`) | Rename by role or meaning |
| Prop synonyms for existing vocabulary (`type` for emphasis, `tag` for `as`) | Use the library's word |
| Boolean defaults to `true`, or is negative | Invert it to a positive adjective defaulting to `false` |
| Selection by array index | Use stable string values |
| Value is controlled-only or uncontrolled-only | Support `value` and `defaultValue`, or state why not |
| Change fires on mount, on same value, or from a prop update | Fire only on user-caused change |
| Duplicated state (stored value plus stored derived value) | Derive it |
| Root drops `className`, `style`, `data-*`, `aria-*`, handlers or ref | Spread and merge onto the root, own ARIA last |
| Native control loses `name`, `required`, `autocomplete` | Pass through all native props |
| One framework differs in props, defaults, DOM or event timing | Align to the contract; diff rendered DOM across all four |
| Vanilla file not a default `createXxx()` returning an element, or leaks listeners | Return a root element, add `destroy()` with AbortController |
| `Math.random()` or counter ids that break SSR | `useId` or stable instance ids |
| Hard-coded color, radius, spacing or duration | Use `--q-*` tokens |
| `component.json` `variables` out of sync with CSS | List exactly the used tokens |
| Style hooks are ad hoc classes | Use `data-variant`, `data-state`, ARIA attributes |
| No role, name source or keyboard model in the spec | Write all three; cite `accessibility` |
| Spec fields missing en or ko, or `apiRows` incomplete | Fill both languages and every public prop and event |
| Non-goals unstated | List them in `spec.md` |
| Rename or default change without alias, warning, doc | Follow the section 9 path |
| Folder id differs from `component.json` `id`, or checks not run | Fix the id; run folder test, `npm run check`, `npm run build` |

## Credits

Written for Quark; complements the skill pack synthesized from Emil Kowalski's and Jakub Krehel's skills (both MIT).
