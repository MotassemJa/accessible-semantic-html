# ARIA Patterns Quick Reference

Based on WAI-ARIA Authoring Practices Guide (APG). Use native elements first — only
reach for these patterns when no native element covers the interaction.

## General rule of thumb

Every custom widget needs three things: a `role` (if not implied by a native element),
a keyboard interaction model, and focus management. Missing any one of these breaks
the pattern for assistive tech users even if it "looks" accessible visually.

## Dialog / Modal

- Prefer native `<dialog>` with `.showModal()` over a hand-rolled overlay.
- On open: move focus to the dialog (or its first focusable element/heading).
- Trap focus within the dialog while open (native `<dialog>` does this for you).
- `Escape` closes the dialog; closing returns focus to the trigger element.
- Label with `aria-labelledby` pointing to the dialog's heading.

## Tabs

- Container: `role="tablist"`. Each tab: `role="tab"`, `aria-selected`,
  `aria-controls` pointing to its panel. Panels: `role="tabpanel"`, `aria-labelledby`.
- Arrow keys move between tabs (`Left`/`Right` for horizontal, `Up`/`Down` for vertical).
- Only the active tab is in the tab order (`tabindex="0"`); others get `tabindex="-1"`.

## Disclosure (accordion / show-more)

- Prefer native `<details>`/`<summary>` for simple cases.
- For custom versions: trigger is a `<button aria-expanded="true|false" aria-controls="...">`.

## Menu / Menu button

- Trigger: `<button aria-haspopup="true" aria-expanded="...">`.
- Menu: `role="menu"`, items `role="menuitem"`.
- Arrow keys navigate items, `Escape` closes and returns focus to trigger,
  typeahead (typing a letter) jumps to matching item.
- Don't use this pattern for simple navigation links — a `<nav>` list is usually right.

## Combobox / Autocomplete

- Input: `role="combobox"`, `aria-expanded`, `aria-controls` (the listbox),
  `aria-activedescendant` (the currently highlighted option, updated via JS not focus-move).
- Listbox: `role="listbox"`, options `role="option"`.
- Arrow keys move the active descendant, `Enter` selects, `Escape` closes.

## Live regions (dynamic content announcements)

- `aria-live="polite"` for non-urgent updates (form validation success, search result counts).
- `aria-live="assertive"` sparingly — only for urgent, time-sensitive info (errors that block progress).
- The live region element should exist in the DOM before content is injected into it;
  injecting the element and its content at the same time often fails to announce.

## Common mistakes to avoid

- Adding `role="button"` to a `<div>` without also adding `tabindex="0"` and keydown handling for `Enter`/`Space`.
- Using `aria-label` to override text that's already visible and correct (redundant/conflicting for screen reader users).
- Setting `aria-hidden="true"` on an element that still contains focusable children.
- Using placeholder text as a substitute for a `<label>`.
