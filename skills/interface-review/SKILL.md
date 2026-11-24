---
name: interface-review
description: Review a diff, component, or screen across every interface domain (motion, polish, typography, color, accessibility, layout, copy, robustness) and return one ranked report with a Block/Approve verdict. Use when asked to review, audit, critique, or sign off on UI work, or to decide whether motion should be added or removed.
license: MIT
---

# Interface Review

One review skill for all interface domains. It owns scope, severity, status, evidence, and the report. Domain rules live in sibling skills: `animate`, `interface-polish`, `typography`, `design-tokens`, `accessibility`, `layout`, `interface-copy`, `stress-test`. Pull exact values from them; do not recreate rules from memory. If a sibling is unavailable, mark that domain `Not reviewed` and say so.

To run every domain against a page or component in one pass, start from `design-check`; it uses this skill for severity and the report.

Correctness, tests, security, and backend performance belong to a general code review. Mention such a concern once, point there, and drop it.

## Evidence, not taste

A trigger is a failure whatever the style guide says. A density, radius, or voice you merely dislike is not a finding. A documented project convention settles taste but never excuses a trigger. The bar for reporting is evidence; the bar for `Approve` is that you inspected what you claim to have inspected. Default to flagging motion: approval is earned.

## 1. Resolve scope first

| Scope | What to review |
| --- | --- |
| Diff (uncommitted or branch) | Read the whole diff before judging. Review the changed files plus the surfaces they render in |
| Component | Every variant and state: hover, focus, active, disabled, loading, selected, empty, error |
| Screen / flow | All of it, including narrow-width, empty, loading, and error states |

State the resolved scope. If it is too large to inspect credibly, narrow to one complete flow (the one the request centers on, or the entry path every user passes) and state what was excluded. Never imply uninspected surfaces were reviewed.

**Blast radius (diff scope).** A changed file is evidence, not the subject. Expand one hop to direct importers/callers; expand a second hop only for tokens, theme values, and shared primitives. Review at most five consumers and state how many you did not expand. Exclude lockfiles, snapshots, generated and vendored files, and name the exclusions.

**Recon.** Identify the framework, styling system, tokens, supported viewports, and any preview/test command. Read project conventions (`CLAUDE.md`, `AGENTS.md`, contributing/design-system docs) and name which you found. Write every fix in the project's own idiom; extend its tokens (`--q-*` in Quark) rather than adding new values or stacks. When a shared token or guideline is the cause, report once against that source and list the components as locations.

Reviews are read-only unless the user also asks you to implement the findings.

## 2. Always-HIGH escalation triggers

Once confirmed, these are `HIGH` on sight and are never averaged down because the surface is minor:

- Interactive control with no accessible name.
- Keyboard-reachable control with no visible focus indicator.
- Control or path reachable by pointer but not by keyboard.
- Movement or auto-playing content that ignores `prefers-reduced-motion`.
- Content or control clipped, overlapped, or unreachable at 320px width or 200% zoom.
- Text or control whose rendered contrast pair fails its required ratio.
- State or meaning carried by color alone.
- Destructive action with no confirmation, undo, or distinct treatment.
- Truncated content with no way to reach the full value.
- Content reachable only past a scroll edge or behind a disclosure with no visible cue.
- Error that names no way to recover.
- Semantic color used against its meaning (danger hue on a non-destructive action).
- State change carried by motion alone, with nothing left behind when the animation does not run.
- Animation on a keyboard-initiated or 100+/day action.
- `transition: all`, `scale(0)` entrances, or `ease-in` on UI interaction.

Triggers rank above everything else. If more fire than the cap allows, list them first and say how many the cap excluded. A cap may shorten a report; it may never be why a blocker went unreported.

## 3. Severity

- **HIGH**: blocks a task, misleads the user, hides content or controls, risks data loss, or is a repeated systemic failure.
- **MEDIUM**: meaningfully harms comprehension, efficiency, adaptability, or consistency.
- **LOW**: isolated polish with limited task impact.

Within a severity, rank by reach (how many places) and leverage (how much one fix buys). A token or shared-component fix outranks the same symptom in one leaf.

## 4. Status (diff scope)

Every finding gets one status, assigned by cause rather than location:

- **Introduced**: the change created it (including an untouched consumer broken by a changed token).
- **Regression**: the change weakened something previously correct.
- **Pre-existing**: present before and not made worse. Report at most three, highest severity first, labeled as not this change's responsibility.

The cap and the verdict count `Introduced` and `Regression` only; a change with only `Pre-existing` findings is an `Approve`. To check whether a line predates the change, blame it against the base.

### Read the removed lines

Regressions are invisible in the post-change state. Read the `-` side of every hunk. A signal is a lead, not a finding: it is a regression only when nothing in the change replaces it.

| Removed | Route to | Check |
| --- | --- | --- |
| `aria-label`, `aria-labelledby`, `aria-describedby`, `aria-live`, `role=` | `accessibility` | Name, description, or announcement lost |
| `alt=`, `<label`, `for`/`htmlFor`, `scope=` | `accessibility` | Image, field, or cell lost its association |
| `<button>`, `<a>`, `<nav>`, `<main>`, `<ul>` replaced by `div`/`span` | `accessibility` | Keyboard and AT behavior traded for styling |
| `:focus-visible`, `outline`, `tabindex` | `accessibility` | Focus indicator or tab-order entry lost |
| `prefers-reduced-motion`, `@media (hover: hover)` | `animate` | Motion ignores the user preference or fires on touch |
| `prefers-contrast` | `design-tokens` | Contrast preference ignored |
| Color token swapped for a literal or a lighter token | `design-tokens` | Measure the rendered pair |
| Logical properties swapped for `left`/`right` | `layout` | Direction-aware layout dropped |
| `lang=`, `dir=` | `typography` | Language or direction metadata dropped |
| `text-wrap`, `line-clamp`, `overflow-wrap`, `tabular-nums`, `font-feature-settings` | `typography` | Wrapping or numeral alignment silently changed |
| `min-width: 0`, `flex-shrink: 0`, `object-fit`, `onerror` fallback | `stress-test` | Robustness guard removed |
| User-facing string deleted or shortened | `interface-copy` | Label, error, or empty state lost information |

**Equivalent replacements clear the signal:** `aria-label` to `aria-labelledby` on visible text; `role="button"` dropped because the element became a `<button>`; `outline` replaced by a `box-shadow` ring on the same `:focus-visible`; `tabindex="0"` dropped from a natively focusable element; literal replaced by a same-value token; physical property to logical; string moved into the translation catalogue.

### Hold the change to its stated intent

Read the PR/commit description and look for the incomplete change: a new variant missing some of hover, focus, active, disabled, loading, selected; a new string with no catalogue entry; a new component with no empty/loading/error/narrow state; a control added to one surface but not its siblings. Do not report scope creep.

## 5. Prefer the cheaper fix

Severity says how bad; this says which fix to propose. Take the earliest that works:

1. **Delete**: the separator space would carry, an animation on a high-frequency action, ARIA a native element makes redundant, an unused ramp.
2. **Use the platform**: the native element, control, or focus ring over a custom rebuild.
3. **Reuse**: an existing token, spacing step, or easing curve before any new value.
4. **Correct the value**: the right easing, radius, gap, or contrast pair, using the exact value the owning skill gives.
5. **Add**: a new token, wrapper, media query, or ARIA attribute the platform cannot supply.

If the code under review adds what step 1 would remove, that is the finding and its After is the deletion.

## 6. Cross-domain checklist

Walk in this order so foundational failures are not hidden by polish. Rules live in the named skill.

| Domain (skill) | Hunt for |
| --- | --- |
| Accessibility (`accessibility`) | Missing names; div buttons; no focus ring; keyboard traps; targets under 24px (44px touch recommended, not a blocker); unlabeled inputs; announcements missing; zoom/reflow breaks |
| Layout (`layout`) | Ungrouped spacing; misalignment; physical properties blocking RTL; no container adaptivity; `100vh` on mobile; missing safe-area |
| Copy (`interface-copy`) | Vague buttons ("OK"); errors with no recovery; blame-y or jargon text; empty states with no next step; untranslated or concatenated strings |
| Typography (`typography`) | Body under 16px; inputs under 16px on mobile; wrong line-height/measure; missing `tabular-nums` on changing numbers; no `text-wrap`; font loading shift |
| Color (`design-tokens`) | Hardcoded literals; contrast pair failures; no dark-mode pair; color-only state; token tiers skipped |
| Polish (`interface-polish`) | Nested radii not concentric; icon not optically centered; borders where shadows belong; images without outline; blur/material misuse |
| Motion (`animate`) | See section 7 |
| Robustness (`stress-test`) | Unbreakable strings; shrinking avatars; wrapped badges; "1 members"; no empty state; broken-image icon; ellipsis with no full text |

When two skills cover one issue, assign it to the owner of the underlying rule and note secondary effects in Why.

## 7. Motion: review, add, remove

### Review standards

Every animation in scope is measured against these. A violation is a finding.

1. **Justified.** It answers "why does this animate?": feedback, spatial consistency, state indication, preventing a jarring change, explanation, or delight (rare moments only).
2. **Frequency-appropriate** (see gate below).
3. **Easing.** Enter/exit `--ease-out: cubic-bezier(0.23,1,0.32,1)`; on-screen move `--ease-in-out: cubic-bezier(0.77,0,0.175,1)`; drawer `--ease-drawer: cubic-bezier(0.32,0.72,0,1)`; hover/color `ease`; constant motion `linear`. `ease-in` on UI is a finding.
4. **Duration.** Press 100-160ms; tooltip 125-200ms; dropdown/select/popover 150-250ms; modal/drawer 200-500ms; other UI at most 300ms; exit faster than enter.
5. **Origin.** Popovers scale from the trigger `transform-origin`; modals stay centered. Entrances from `scale(0.9-0.97)` + opacity, never `scale(0)`. Press feedback `scale(0.97)` (0.95-0.98) on `:active:not(:disabled)`, `transition: transform 160ms var(--ease-out)`.
6. **Interruptible.** Rapid or reversible motion uses transitions or springs, not restarting keyframes.
7. **Cheap properties.** `transform`/`opacity` (and `clip-path`, measured-height accordions as exceptions). No layout properties, no `transition: all`, no parent CSS variable driving child transforms, blur under 20px, `will-change` only as last resort.
8. **Reduced motion.** Replace movement with an opacity fade; do not zero everything. Hover motion gated by `@media (hover: hover) and (pointer: fine)`.
9. **Asymmetric.** Deliberate phases (press, hold, confirm) slow; system responses snap.
10. **Cohesion.** Shared easing/duration tokens, not five near-identical cubic-beziers; personality matches the product. When unsure it feels right, the strongest move is to delete.

Stagger: list items 30-60ms, total at most 300ms; never on high-frequency or keyboard-triggered UI; exits simultaneous.

### Frequency gate (decide first, for adding or removing)

```
Keyboard-initiated (shortcut, palette, arrow navigation, focus jump)?  -> none
100+/day (core nav, tab switches, list rows, in-app route changes)?    -> none
On functional data the user reads or acts on (charts, tables, numbers)? -> none, unless it shows a real value change
Purpose nameable (feedback / spatial / state / jarring change / explanation)?
  no  -> rare first-time moment? keep as delight : delete
  yes -> tens/day? reduce to <=150ms opacity/background : over budget? reduce : keep
```

### Suggesting motion to add (restraint first)

Expect to reject most candidates. Each suggestion must pass frequency, purpose, speed (inside the duration budget), and function (motion helps rather than hinders). Cap at 5-7 for a whole app, fewer for a view, ordered by leverage, with exact curve/duration/properties from the tokens above. Typical seams: pressable elements with no `:active`; content that teleports (conditional renders, accordions); popovers with no trigger-origin; grid/list entrances (stagger); drag with no spring/velocity; flat rare moments (first-run, success). Always list up to 5 **rejected candidates** with the gate that killed each (for example "command palette open/close: keyboard-initiated, 100+/day").

### Removing or reducing motion

| Today | Reduce to |
| --- | --- |
| Hover scale, lift, or shadow growth | Color/opacity only, at most 150ms |
| Slide or scale entrance on a frequent surface | Opacity only, 150ms |
| UI animation over 300ms (not modal/drawer/sheet) | 200-250ms, same curve |
| Stagger over 60ms per item or 300ms total | 30ms per item, or fade the group as one |
| Infinite loop on a persistent element | Run once, or keep the static end state |
| Spinner or skeleton for work under 300ms | Remove; the flash reads as a glitch |

If reducing leaves motion too small to carry its purpose, delete it. Also hunt: scroll-reveal on every section, charts redrawing on every mount, count-up on page load, `transition: all`.

**Removal hazards** (check before deleting):

- Hidden start states (`opacity: 0` initial, `fill-mode: forwards`, reveal classes): remove them with the animation or the element stays invisible.
- Code waiting on the animation (`animationend`/`transitionend`, exit-complete callbacks, timeouts sized to a duration): delete the wait too.
- Exit animations that delayed unmount: move cleanup to the close point.
- Layout animations masking a jump: keep and list the shift.
- Focus moved in an animation callback: move it immediately.
- Tests or selectors keyed on animation classes or durations.

Then delete orphans (unused keyframes, motion wrappers with no props, unused imports) and verify nothing references removed names. Do not remove a dependency without asking.

## 8. Verify and cap

Run safe, relevant checks the project offers (type check, lint, tests, build). Inspect the rendered interface when runtime behavior or visual judgment matters, and report the exact command or interaction and result. A check you cannot run is **Not verified**, never a finding. Feel cannot always be judged from code; say so.

Every finding cites `path/file:line` and shows the current implementation; source alone cannot support a visual finding and a screenshot alone cannot support a code finding. Consolidate: one root cause is one finding with every location in the row. Report at most 15 findings and never pad toward the cap. No findings is a valid result.

## 9. Report format

**Scope block:** target, base/head (for a diff), files in scope after exclusions, excluded paths, surfaces expanded and consumers skipped, stack and conventions found.

**Coverage table** (include every domain; `Clear` means inspected with no actionable finding):

| Domain | Evidence inspected | Result |
| --- | --- | --- |
| Accessibility | `Dialog.tsx`, focus and keyboard pass | 1 finding |
| Typography | Not reviewed: sibling skill unavailable | Not reviewed |

**Findings** (one table, severity then reach; add a `Status` column for diffs):

| Severity | Domain | Status | Location | Before | After | Why |
| --- | --- | --- | --- | --- | --- | --- |
| HIGH | Accessibility | Regression | `src/Dialog.tsx:42` | `aria-label="Close"` removed | Restore `aria-label="Close"` | Icon-only control lost its accessible name |
| HIGH | Motion | Introduced | `Menu.css:12` | `transition: all 300ms ease-in` | `transition: opacity 150ms var(--ease-out)` | Keyboard-opened menu; `all` and `ease-in` delay response |

With none: "No actionable interface findings."

Then, as relevant: **Pre-existing** (at most three), **Motion opportunities** (with rejected candidates), **Motion removed/reduced** (with deciding branch and kept list), **Verification** (checks passed, checks Not verified).

**Verdict:** end with exactly one.

- `Block`: one or more `HIGH` findings remain.
- `Approve`: no `HIGH` findings remain; `MEDIUM` and `LOW` stay as work to do. Name every `Not reviewed` domain in the verdict line, since `Approve` covers only what was inspected.

## Before you finish

| Mistake | Fix |
| --- | --- |
| Separate section per domain | One merged table ranked by severity, then reach |
| Missing name or focus ring rated MEDIUM on a minor surface | Triggers are HIGH on sight |
| Coverage row with no named evidence | Inspect it or mark `Not reviewed` with the reason |
| Same symptom in several rows | One row against the shared token/component, all locations listed |
| After that adds a wrapper, token, or ARIA | Check delete and native element first |
| Fix written for a stack the project does not use | Rewrite in the project's idiom |
| Rendering claim backed only by source | Inspect it or mark Not verified |
| Removed attribute flagged though replaced in the same diff | Check equivalent replacements |
| Finding on an untouched consumer of a changed token marked Pre-existing | Cause decides status: Introduced/Regression |
| Motion wishlist with no rejected list | Add rejected candidates and the gate that killed each |
| Animation deleted but its start state left behind | Remove `opacity: 0` initials and waiting callbacks |
| Review turned into edits unasked | Report first; implement only on request |

## Credits

Synthesized for Quark from Emil Kowalski's skills (github.com/emilkowalski/skills) and Jakub Krehel's skills (github.com/jakubkrehel/skills), both MIT.
