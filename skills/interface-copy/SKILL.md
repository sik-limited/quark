---
name: interface-copy
description: Write and review interface copy (labels, buttons, links, errors, empty states, confirmations, status text, placeholders, translatable strings) so it matches the product voice and tells people what to do next. Use when writing or reviewing UI text, error messages, dialogs, toasts, empty states or i18n strings.
license: MIT
---

# Interface Copy

Use this when writing or reviewing UI text. A deliberate brand voice is not a defect: flag a departure from plain language only when it causes inconsistency, ambiguity, translation risk or a tone the stakes do not support. Taste-based rewording is not a finding. Rendering (`text-transform`, truncation) belongs to `typography`; error markup and accessible-name attributes to `accessibility`; room for longer translations to `layout`.

## 1. Inventory first

Before writing, find where copy lives and read what surrounds the change.

1. Locate the translation call (`t(`, `$t(`, `<FormattedMessage`, `i18n.`) and locale files (`messages/*.json`, `locales/**`, `*.po`). Copy may also come from a CMS or API.
2. List the noun used per object ("project" vs "workspace") and the verb per action ("Delete" vs "Remove").
3. Note the case used per element type and any style guide.

New copy reuses those terms. A synonym is a finding only when one thing has two names ("Archive" in the menu, "Move to storage" in the toast).

## 2. Voice and tone

One voice per product; a multi-step flow uses one vocabulary ("Get started", then "Continue" or "Next", then "Done"). Tone follows stakes.

| Context | Tone |
| --- | --- |
| Success, onboarding, empty states | Warm, can be light |
| Routine actions, settings | Neutral, minimal |
| Errors, destructive confirmations | Calm, plain, no playfulness |
| Data loss, security | Serious, explicit |

- Say "you", not "the user". In errors, avoid "we" (reads as deflection).
- Possessives sparingly: "Favorites", not "Your favorites". Never mix "My account" with "Your settings".
- Plain words a tired reader gets first pass. Delete words that do no work. No idioms, colloquialisms or humor that will not translate. Cut "please" and "successfully" from routine text.
- Avoid gendered phrasing. Match the device: "tap" on touch, "click" with pointer, "select" when both.

## 3. Buttons, links, labels

- Buttons start with a verb naming the action: "Send", "Save draft", "Delete project". Never "OK", "Yes/No", "Let's go!". Icon-only buttons are named the same way: the action ("Delete project", "Close dialog"), never the glyph ("Trash icon", "X").
- Links make sense out of context: "Read the billing docs", never "Click here". Two "Learn more" on one page get suffixes: "Learn more about exports".
- Pick title case or sentence case per element type and apply it everywhere. Sentence case is the default when the project has no policy. "Save Changes" beside "Discard changes" reads as sloppy.
- Link straight to a referenced setting ("Notification settings") rather than describing a path ("Settings > Notifications > Email").
- Toggles describe the ON state: "Send read receipts", not "Don't send read receipts" (double negative).
- Placeholders show a realistic example in the accepted format (`name@example.com`, `DD/MM/YYYY`), never an instruction, and never replace a label.

## 4. Errors and hints

An error is an instruction placed beside the field that failed. Say what is wrong and how to fix it. No blame, no "Oops", no exclamation marks.

| Bad | Good |
| --- | --- |
| That password is too short | Choose a password with at least 8 characters |
| Invalid date | Enter a date as DD/MM/YYYY |
| Oops! Something went wrong. | Unable to save. Check your connection and try again. |

- Phrase hints positively: "Use at least 8 characters", not "Don't use fewer than 8". Show known requirements as helper text before input, not only in the error.
- If the same error keeps firing, redesign the interaction so it cannot happen.
- An error that names no way to recover is always a blocker.

## 5. Undo beats confirmation

- Reversible and frequent (archive, move, delete into trash): act at once, then offer undo in the result: "Project archived. Undo".
- Confirm only when the action is irreversible, affects other people or destroys many items. The dialog must be answerable without reading the body.

| Situation | Copy |
| --- | --- |
| Irreversible, one item | Title "Delete 'Q3 report'?" Body "This permanently deletes the report and its 4 comments." Buttons `Delete report` / `Cancel` |
| Irreversible, many | Title "Delete 12 files?" Body "You can't undo this." Buttons `Delete 12 files` / `Cancel` |
| Affects others | Body names them: "The 8 members of Design lose access." |
| Account, workspace, repository | Label "Type 'acme-web' to confirm"; confirm button stays disabled until it matches |

Title names action and object, never "Are you sure?". Body states what is lost and what cannot be recovered, with counts. Buttons are verb plus object. How long an undo toast stays is in `accessibility`; the destructive button's color is in `design-tokens`.

## 6. Empty states

Say what this place is and how to fill it, with one clear next action. A bare "No results" is a shrug.

```html
<p>No projects yet</p>
<p>Projects keep your tasks and files together.</p>
<button>Create a project</button>
```

Filtered empty: name the query and offer an exit: "No results for 'quarterly'. Clear filters". Never park persistent information in an empty state; it vanishes once content exists.

## 7. Status copy

| State | Pattern | Example |
| --- | --- | --- |
| In progress | -ing verb + object | "Saving changes…", "Loading invoices…" |
| Done | Object + past tense, no "successfully" | "Changes saved", "Invite sent to sam@example.com" |
| Failed | What did not happen, then next step | "Changes not saved. Check your connection and try again." |
| Unavailable | Why and what unlocks it, beside the control | "Add a payment method to publish" |
| Partial | Counts for both outcomes | "9 of 12 files uploaded. Retry 3 failed files" |

Keep in-progress and done strings parallel ("Saving changes…" resolves to "Changes saved").

## 8. Internationalization

- Build strings whole. Never concatenate around a variable (`"You have " + n + " messages"`); word order changes per language. One message per sentence with placeholders; the translator moves them.
- Plurals use the locale's rules (ICU `plural`), not `n === 1 ? "" : "s"`. Many languages have more than two forms.

```text
inbox.count = {count, plural,
  =0 {No new messages}
  one {# new message}
  other {# new messages}
}
invite.sent = {name} invited you to {project}
```

Without a library: `new Intl.PluralRules(locale).select(count)`. Never hand-build numbers, dates or lists; pass the user's locale to `Intl`.

| Value | Call |
| --- | --- |
| Currency | `new Intl.NumberFormat(locale, { style: "currency", currency: "EUR" })` |
| Unit | `new Intl.NumberFormat(locale, { style: "unit", unit: "megabyte" })` |
| Date | `new Intl.DateTimeFormat(locale, { dateStyle: "medium" })` |
| Relative time | `new Intl.RelativeTimeFormat(locale, { numeric: "auto" }).format(-1, "day")` |
| List | `new Intl.ListFormat(locale, { type: "conjunction" })` |

Keep strings free of embedded markup order assumptions; leave room for 30-40% growth (`layout`).

## Before you finish

| Pattern | Fix |
| --- | --- |
| "Click here", bare "here", duplicate "Learn more" | Name the destination |
| "Oops", "Something went wrong", "!" in an error | Say what failed and the next step |
| `" + n + "`, sentence fragments around a variable, `=== 1 ? "" : "s"` | One ICU message with a plural argument |
| `OK`, `Yes`, `No` on a dialog button | Verb plus object |
| "Are you sure" | Name the action and the object |
| "successfully" | "Changes saved" |
| "Please" in a routine instruction | Cut it |
| Toggle starting "Don't", "Disable", "Hide" | Describe the ON state |
| `aria-label` of "Trash", "Close icon", "X" | Name the action |
| `toLocaleDateString()` with no locale, dates built from `getMonth()` | `Intl.DateTimeFormat` with the user's locale |
| "Save Changes" beside "Discard changes" | One case per element type |
| Destructive action with neither confirmation nor undo | Add undo (reversible) or confirmation (irreversible) |

Severity: HIGH misleads the user or hides how to recover (no-fix errors, unguarded destructive actions). MEDIUM breaks voice, terminology or capitalization consistency. LOW is isolated wording polish. Read locale files, not just components; report server or CMS strings you could not see as not verified.

## Credits

Synthesized for Quark from Jakub Krehel's skills (github.com/jakubkrehel/skills), MIT.
