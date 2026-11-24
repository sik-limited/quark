---
name: q-clean
description: All-in-one cleanup of the UI the user points at. Detects the project's framework and folder conventions, componentizes the target (skipping what is already a component), then runs a parallel swarm of read-only leaf workers for state, motion, responsive, web-standard accessibility and restraint, each driven by Quark skills, applies the merged fixes without changing borders, margins, radii, padding, gaps or any other design value, and scores the result on a 15-item checklist. Use when asked to "clean this up", "q-clean", "정리해줘", "tidy this component", "make this production ready", or to fix state, animation, responsiveness and accessibility of a component or page in one go.
license: MIT
---

# Q Clean

One command, one target, one finished diff. Q Clean is the conductor: it owns scope, the design lock, the merge and the score. The rules live in the domain skills; leaf workers load them and propose patches; only the conductor edits files.

For a read-only audit use `design-check`. To run part of Q Clean, name the workers: "q-clean the card, state and accessibility only" runs those two and scores the rest `N/A`.

## 0. The design lock (read first, never relax)

The user's design is the spec. Cleaning changes structure and behavior, never the look.

**Frozen** on the target and every descendant, including `::before` / `::after`, at every captured width, theme and state:

| Group | Properties |
| --- | --- |
| Box | `margin`, `padding`, `gap`, per-side `border-*-width/style/color`, per-corner `border-*-radius`, `box-sizing`, resting `outline` |
| Size | `width`, `height`, `min-*`, `max-*`, `aspect-ratio`, `flex-grow/shrink/basis` |
| Position | `position`, `inset` / `top` / `right` / `bottom` / `left`, `z-index`, `overflow`, resting `transform` |
| Type | `font-family`, `font-size`, `font-weight`, `line-height`, `letter-spacing`, `text-transform`, `text-align`, `white-space`, `text-overflow` |
| Paint | `color`, `background`, `box-shadow`, `opacity`, `filter`, `backdrop-filter`, `visibility`, `content` |
| Layout | `display`, `flex-wrap`, flex/grid tracks, `align-*`, `justify-*`, `order`, element order on screen, which elements are flex/grid items |

**Allowed** without asking, because the resting look stays identical. Each still has to pass the diff in section 7.

- Styles for a state the design never defined: a `:focus-visible` ring (`outline` or `box-shadow` only, negative `outline-offset` if an ancestor clips it), `:active` press feedback, `[aria-invalid]`, `[aria-busy]`, and `:disabled` only if the design has no disabled style (reuse an existing muted token, add no new color). Existing `:hover`, `:focus` and `:disabled` styles are frozen.
- Branches that apply only under a preference or input type: `prefers-reduced-motion`, `prefers-contrast`, `forced-colors`, and wrapping existing hover styles in `@media (hover: hover) and (pointer: fine)`.
- `min-width: 0` on a flex/grid child and `overflow-wrap: break-word` on user text (not `anywhere`, which changes min-content sizing), but only if the element rects stay identical within 0.1px at every captured width with the design's data.
- Hit-area growth through `::before` / `::after` with negative inset, but only if the host is already positioned, has no `overflow` clip, has no existing pseudo-element on that side, and the grown area overlaps no neighbouring target (check with `document.elementFromPoint`).
- Replacing a literal with an existing token, but only if the computed value is identical in every shipped theme and the unit matches (`rem` and `px` diverge under text zoom).

**Needs approval** (report it, do not apply it): any change to a frozen value. Typical cases: a target under 24px that cannot grow by pseudo-element, text contrast below 4.5:1, an input under 16px on iOS, a fixed height that clips real content, overflow at 320px, `flex-wrap`, `container-type` on a host that has none (size containment can collapse a shrink-wrapped host), `vh` → `dvh`, safe-area insets, `position: relative` added for a hit area. Put each under "Design decisions for you" with the smallest change that would fix it.

**Adding or removing an element** is lock-neutral only if its parent is block flow, its own computed style is all initial, and no sibling, child or `:nth-*` selector can see it. Inside a flex or grid parent, or with `display: contents`, it needs approval: it changes which nodes take `gap` and `order`.

**Shared CSS** (a stylesheet several components or framework variants use) is frozen for all of them. Never fork it to fix one consumer.

## 1. Load the skills, never recall them

Before a worker runs, load its skills, in this order: the repository copy (`src/lib/items/skills/<id>/SKILL.md` inside Quark), the installed copy, then `https://design.sik.limited/skills/<id>/SKILL.md`. Apply the values from the loaded text, not from memory.

If a skill cannot be loaded, that worker checks only these always-HIGH triggers and marks itself `Partial`: no accessible name; no visible focus; unreachable by keyboard; ignores `prefers-reduced-motion`; clipped or overflowing at 320px or 200% zoom; text contrast fails; meaning carried by color alone; state lost or wrong after an interaction.

## 2. Resolve the target

Clean only what the user named: a component, a selector, a section, a route. Name it back in one line before editing. Match names in this order: a component library's own id (`component.json` `id` / `name`), then a file or component name, then visible text. If two candidates are equally likely, ask. Otherwise pick the one the user was last working in and say so.

**Variant sets.** If the target is one component shipped as several framework files plus shared CSS (Quark's `preview.svelte`, `react.jsx`, `vue.vue`, `vanilla.js`, `style.css`), the whole set is the target. The host framework's file (the one the site renders) is the reference. A behavior fix applies identically to every variant that has the bug, and the variants must still render the same DOM and class names afterwards. Never add a variant the library does not list.

**Out of scope:** siblings, shared primitives the target imports (`.q-button`, a global popup class), global styles, tokens. A problem found there goes to "Also noticed". If a fix can only live there, list it under "Design decisions for you".

## 3. Recon once, share with every worker

Read, do not guess. Produce a recon card:

| Field | How to find it |
| --- | --- |
| Framework and version | `package.json`: `svelte`, `@sveltejs/kit`, `react` (18/19), `next`, `vue`, `nuxt`, `solid-js`, `@angular/core`, none = vanilla |
| Component idiom | The two nearest sibling components decide, not the version: Svelte 5 files may still use legacy syntax; Vue 3 files may use the Options API. Record file naming, export style, props, events (`onclick` / `onClick` / `emit`), children (slots, snippets, `children`) |
| Folder convention | Where siblings live (`src/lib/components`, `components/ui`, colocated folders), barrel files, tests or stories next to components |
| Library item | If the target has a manifest (`component.json`, a spec folder), read it and its spec: documented limitations are not bugs, and the fixed file set is the convention. Never create new files there |
| Styling | Scoped `<style>`, CSS Modules, Tailwind (version), CSS-in-JS, global CSS; token source (`--q-*` in Quark, theme file, Tailwind config); every shipped theme |
| Motion available | Installed only: CSS, `svelte/transition` / `svelte/motion`, Vue `<Transition>`, `motion`, View Transitions |
| State available | Framework primitives, stores, Pinia, Zustand, Redux, signals, form libraries |
| Commands | Typecheck, lint, test, dev server; which of them actually cover the target files |
| Rendering | Dev server URL for the target; whether a browser tool is available |

Write fixes in the project's idiom. Never add a dependency. Never adopt a pattern the siblings do not use.

## 4. Componentize (or skip)

Skip this section for an existing component or a library item.

1. **Repeated markup** (the same structure two or more times in scope) or a **self-contained piece with its own state or behavior** (toggle, menu, form field, card with actions): extract one component.
2. **One-off static markup** with no state: leave it inline. Extracting it is the over-engineering Q Clean removes.

When extracting, copy the nearest sibling exactly: file name casing, folder, export, props style, children, style placement, tests or stories beside it. Props come from what actually varies between call sites. Name it after what it is (`PlanCard`), not where it is (`HomeSection2`). `component-api` decides naming, variant vs prop, and controlled vs uncontrolled; its Quark-only rules (`component.json`, `q-<name>`) apply only inside Quark.

Extraction must not move the cascade:

- Keep class names. Move the style rules that target the moved markup with it.
- Svelte drops scoped rules that no longer match markup in the same file and adds a hash class (more specificity); Vue `scoped` reaches only the child's root (use `:deep()`); CSS Modules `composes` does not cross files; Tailwind v4 `@apply` in a component style needs `@reference`, and new paths must be inside the content scan. Moving rules between files changes source order.
- Check parent selectors, `:global()`, `:deep()`, descendant, sibling and `:nth-*` selectors that reached into the markup.
- Run the section 7 diff right after extraction, before the swarm's edits, so an extraction regression is never confused with a fix.

## 5. The swarm

Five leaf workers run **in parallel** against the same recon card, file list and lock. If subagents are available, spawn all five in one message with read-only tools (read, search, shell for inspection; no edit tools). Otherwise run them in this order yourself; the contract is the same.

| Worker | Loads | Owns |
| --- | --- | --- |
| **State** | `component-api`, `stress-test` | Idiomatic state per the recon card. Svelte 5 runes: `$state`, `$derived`, `$props`, `$bindable` (required for `bind:` on a prop), callback props instead of `createEventDispatcher`. React: `useState` / `useReducer`, controlled and uncontrolled, reset with `key` instead of syncing in an effect, `useId` for label and error ids, React 19 `ref` as a prop. Vue: `ref` / `computed`, `defineProps` / `defineEmits`, `defineModel` only on 3.4+. Solid: signals. Angular: `input()` / `output()` / `model()`. Vanilla: one state object, one render. One source of truth; derived values derived. Every reachable state present: default, hover, focus-visible, active, disabled, loading, empty, error, selected. Worst-case data: empty, one, many, long, error. |
| **Motion** | `animate` | Frequency gate first: keyboard-initiated and 100+/day actions get no animation. Only motion the project already has. Transform and opacity, the skill's easing and duration table, interruptible transitions over keyframes, exit faster than enter, origin-aware popovers, press feedback, `prefers-reduced-motion` fallback to opacity. |
| **Responsive** | `layout`, `typography` | 320px reflow and 200% zoom without overflow or clipping, `min-width: 0`, wrapping and truncation of real text, hover gated to fine pointers. Container queries, `dvh` and safe areas only as approval items unless already present. |
| **Accessibility** | `accessibility`, `interface-copy` | Native element first, accessible names (this worker owns them; `interface-copy` only rewords), roles and state attributes, keyboard model per APG pattern, visible focus at 3:1, focus moved into and restored from overlays, 24px targets by hit area, labels and error association, `aria-live` / `role="status"` for async status, no ARIA that duplicates native semantics. |
| **Restraint** | `interface-review`, `design-tokens` | What to remove: wrappers that do nothing (subject to the element rule in section 0), props with one value at every call site, duplicate state, effects that mirror props, animations that fail the gate, redundant ARIA, one-caller abstractions, literals where a token exists (identical computed value only), dead code and unused imports. Reviews the other four workers' proposals and may veto additions. |

`interface-polish` is not a worker: its concentric radius, shadow-as-border and optical nudges change frozen values. Load it only to flag details under "Design decisions for you".

Each worker's prompt is self-contained, because workers do not inherit this skill: paste the full text of its loaded skills (or their paths with "read these first"), the recon card, the file list, the section 0 lock and this schema:

```
id · worker · severity (HIGH | MEDIUM | LOW) · file
anchor: exact current text to replace, one or more whole lines, unique in the file
after: full replacement text
variants: all | <list>        (variant sets only)
depends_on: <ids> | none
why: the skill rule and value it applies
lock impact: none | allowed:<which allowance> | needs approval
verify: what to observe after applying it
```

Cap per worker: `max(3, target lines ÷ 10)`, at most 12, highest severity first, always-HIGH triggers first. Workers merge their own proposals that touch the same lines.

## 6. Merge and apply

1. Move every `needs approval` proposal to the report.
2. Collapse duplicates across workers: one root cause is one change with every location.
3. Resolve conflicts by precedence: design lock > accessibility > state correctness > restraint > responsive > motion. A veto counts only if it cites the skill rule it relies on, and it does not stand against an addition accessibility or correctness requires. When two proposals edit the same anchor, apply the winner and recheck the loser against the new text before dropping it. Every dropped proposal is listed with its reason.
4. Apply by exact `anchor` match, `depends_on` first, HIGH first, shared code in scope before call sites. An anchor that is missing or no longer unique means the proposal is stale: ask its worker to reissue it against the current file.
5. With more than 30 merged proposals, apply HIGH only and report the rest.
6. Smallest diff: no reformatting, no renames outside scope, no new files except an extracted component. Match the surrounding style and the project's formatter.

## 7. Verify

1. **Commands.** Run typecheck, lint and tests from the recon card. Fix any failure the edits caused before anything else. Report pre-existing failures as pre-existing. If no test covers the target, write `no covering test`, never ✓. Check variants the typechecker skips by the cheapest real means (`node --check` for plain JS, the bundler for JSX); otherwise mark them `syntax unchecked`.
2. **Design lock diff.** With a browser, open the target on the dev server and capture before the first edit, after extraction, and after the last edit. Disable transitions (`*{transition:none!important;animation:none!important}`) and `await document.fonts.ready` first. Capture at 320, 768, 1280 and each project breakpoint ±1px; in every shipped theme; in rest, hover, focus-visible and active (force states with the browser tool or DevTools); with the design's data and with worst-case data. Use this snapshot:

```js
const P = 'marginTop marginRight marginBottom marginLeft paddingTop paddingRight paddingBottom paddingLeft rowGap columnGap borderTopWidth borderRightWidth borderBottomWidth borderLeftWidth borderTopStyle borderTopColor borderRightColor borderBottomColor borderLeftColor borderTopLeftRadius borderTopRightRadius borderBottomRightRadius borderBottomLeftRadius boxSizing width height minWidth minHeight maxWidth maxHeight flexGrow flexShrink flexBasis position top right bottom left zIndex overflowX overflowY transform display flexWrap gridTemplateColumns gridTemplateRows alignItems justifyContent order fontFamily fontSize fontWeight lineHeight letterSpacing textTransform textAlign whiteSpace textOverflow color backgroundColor backgroundImage boxShadow opacity filter backdropFilter visibility outlineStyle outlineWidth content'.split(' ');
const qSnap = (root) => {
	const out = {}, seen = {}, base = root.getBoundingClientRect();
	for (const el of [root, ...root.querySelectorAll('*')]) {
		const tag = el.tagName.toLowerCase() + '.' + [...el.classList].join('.');
		const key = tag + '#' + (seen[tag] = (seen[tag] ?? -1) + 1);
		for (const pseudo of [null, '::before', '::after']) {
			const cs = getComputedStyle(el, pseudo);
			if (pseudo && cs.content === 'none') continue;
			const entry = Object.fromEntries(P.map((p) => [p, cs[p]]));
			if (!pseudo) { const r = el.getBoundingClientRect(); entry.rect = [r.x - base.x, r.y - base.y, r.width, r.height]; }
			out[key + (pseudo ?? '')] = entry;
		}
	}
	return out;
};
const qDiff = (a, b, tol = 0.1) => [...new Set([...Object.keys(a), ...Object.keys(b)])].flatMap((k) =>
	!a[k] || !b[k] ? [[k, 'added/removed']] : Object.keys(a[k]).filter((p) => p === 'rect'
		? a[k].rect.some((v, i) => Math.abs(v - b[k].rect[i]) > tol) : a[k][p] !== b[k][p]).map((p) => [k, p, a[k][p], b[k][p]]));
```

   Every diff line must match an allowance in section 0 (an added focus ring in the focus-visible capture, an added hit-area pseudo-element). Anything else is a lock break: revert it or move it to "Design decisions for you". Elements pair by tag, classes and order, so an added or removed element shows as `added/removed` and must be justified. For an extracted component in an SSR framework, also compare the server HTML with the hydrated DOM. Without a browser, diff the CSS and markup by hand, tag the result `Inferred`, and score item 15 at most 1.
3. **Render check.** Keyboard path (Tab, Shift+Tab, Enter, Space, Esc, arrows), `prefers-reduced-motion: reduce`, 200% zoom, the State worker's worst-case data.
4. **Rerun.** Round 1 is the swarm plus apply. Round 2 reruns only the workers whose files or properties were edited, against the edited diff only, and may fix regressions, not add scope. Stop when a round returns no HIGH or MEDIUM proposals, or after round 2.

## 8. Score: the 15-item checklist

Score twice: the baseline from the unedited code after recon (tag it `Inferred` unless rendered), and the result after verify. Each item is 0 (fails), 1 (partial) or 2 (passes), with the evidence behind it: a capture, command output or `file:line`. An item the target cannot reach (no async state, no motion) is `N/A` and leaves the maximum. A fix that needs a frozen change scores at most 1 and appears under "Design decisions for you".

| # | Check | 2 means |
| --- | --- | --- |
| 1 | Scope | Only the named target (and all its variants) changed; out-of-scope findings listed, not fixed |
| 2 | Conventions | Framework idioms of the siblings, file naming, folder and export style match |
| 3 | Componentization | Extracted where repeated or stateful, skipped where already a component, nothing one-off extracted |
| 4 | State model | One source of truth, derived values derived, controlled/uncontrolled correct, no effect-synced state |
| 5 | State coverage | Every reachable state exists: default, hover, focus-visible, active, disabled, loading, empty, error, selected |
| 6 | Real data | Empty, one, many, long and error data render without breaking layout |
| 7 | Motion purpose | Every animation passes the frequency gate; keyboard-initiated and 100+/day actions are instant |
| 8 | Motion craft | Transform/opacity, the skill's easing and duration, interruptible, exit faster than enter, origin-aware |
| 9 | Reduced motion | Movement replaced by opacity or nothing under `prefers-reduced-motion: reduce` |
| 10 | Responsive | 320px reflow and 200% zoom without overflow or clipping; hover gated to fine pointers |
| 11 | Semantics | Native elements, accessible names, correct roles and states, labels tied to inputs |
| 12 | Keyboard and focus | Full keyboard operation per APG, visible 3:1 focus, focus moved and restored for overlays |
| 13 | Feedback | Errors tied to fields, async status announced, targets at least 24px by hit area |
| 14 | Restraint | No new dependency, dead prop, empty wrapper, redundant ARIA or decorative motion; net code not larger without a reason |
| 15 | Design lock | Zero unexplained diff at every captured width, theme and state; every needed change listed for approval |

**Pass** needs the result at 85% or more of the applicable maximum, no 0 on items 1, 11, 12 or 15, and no item lower than its baseline. Anything else after two rounds is `Partial`, with the blocking items named.

## 9. Report

```
Q Clean · <target> · <framework/version> · <Pass | Partial>
Score: <baseline>/<max> → <result>/<max>  (N/A: <items>)
Variants: svelte ✓ · react ✓ · vue ✓ · vanilla ✓  (DOM parity: Observed | Inferred)   ← variant sets only

Changed
| # | File | Change | Worker | Item |

Checklist
| # | Check | Before | After | Evidence |

Design decisions for you
| Element | Issue | Smallest change that fixes it |

Declined proposals
| id | Worker | Reason |

Also noticed (out of scope)
- ...

Verification: typecheck ✓ · lint ✓ · tests ✓ | no covering test · design-lock diff: 0 unexplained (Observed | Inferred)
```

Report what changed and why. No per-worker sections.

## Before you finish

| Mistake | Fix |
| --- | --- |
| Padding, margin, gap, radius or border nudged "to clean it up" | Revert; the lock is absolute. Report it under design decisions |
| Hit area grown with padding, size or a new `position` | Positioned pseudo-element on an already positioned host, or an approval item |
| Wrapper removed inside a flex or grid parent | Restore it; it changes `gap` and `order`. Approval item |
| `container-type`, `dvh` or `flex-wrap` added silently | Approval items |
| `interface-polish` rules applied | It only flags; its fixes change frozen values |
| Workers editing files in parallel | Workers propose; only the conductor edits |
| Worker prompt without the skill text | Paste the skill or its path; workers do not inherit this skill |
| Rules quoted from memory | Load the skill; cite its value |
| One framework variant fixed, the others left | Apply to every variant with the bug; check DOM parity |
| New dependency for an animation | Use what the project has, or CSS |
| One-off markup extracted into a component | Inline it back |
| Runes or Composition API assumed from the version number | The siblings decide the idiom |
| Scoped styles left behind after extraction | Move the rules with the markup; diff right after extraction |
| Something outside the target fixed | Revert; list it under "Also noticed" |
| Score without evidence, or tests ✓ when none cover the target | Cite evidence; write `no covering test` |
| `Pass` with a 0 on scope, semantics, keyboard or design lock | It is `Partial`; name the item |

## Credits

Orchestrates Quark's skill pack, which is synthesized from Emil Kowalski's skills (github.com/emilkowalski/skills) and Jakub Krehel's skills (github.com/jakubkrehel/skills), both MIT.
