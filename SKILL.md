---
name: accessible-semantic-html
description: Generate accessible, semantic HTML, CSS, and modern JavaScript that follows current web standards and WCAG guidelines. Use this whenever writing, reviewing, or refactoring frontend code — components, forms, pages, or UI snippets — to ensure correct semantic elements, ARIA usage, keyboard navigation, accessible styling (focus states, motion/contrast preferences), and up-to-date browser APIs instead of outdated or hacky patterns. Trigger even if the user doesn't explicitly say "accessible" or "semantic" — apply these standards by default to any frontend code output.
---

# Accessible Semantic HTML

Write HTML, CSS, and JavaScript the way the platform intends: semantic markup first, ARIA only where native elements fall short, styling that respects user preferences, and current (not legacy) web APIs.

## Core principles

1. **Native elements over ARIA.** Use `<button>`, `<nav>`, `<dialog>`, `<details>`, etc.
   instead of `<div>` + `role` + JS reimplementation, whenever a native element covers
   the need. ARIA patches gaps — it isn't the first tool.
2. **Semantic structure always.** Correct heading hierarchy, landmark elements
   (`<header>`, `<main>`, `<nav>`, `<footer>`, `<aside>`), and meaningful element
   choice over generic `<div>`/`<span>` soup.
3. **Keyboard and screen-reader parity.** Anything clickable is focusable and
   operable by keyboard. Custom widgets follow WAI-ARIA Authoring Practices
   (see references/aria-patterns.md) for expected key behavior.
4. **Styling respects user context, not just aesthetics.** Visible focus states,
   sufficient contrast, and honoring `prefers-reduced-motion` / `prefers-color-scheme`
   / `prefers-contrast` are defaults, not extras (see references/accessible-css.md).
5. **Current platform APIs.** Prefer modern, well-supported standards over
   older patterns or unnecessary libraries (see references/modern-apis.md
   for current defaults and what they replace).
6. **Progressive enhancement mindset.** Core content/functionality works
   without JS where reasonable; JS enhances rather than gate-keeps.

## Workflow

1. Identify the UI pattern being built (form, nav, modal, table, tabs, etc.)
2. Check references/aria-patterns.md if it's an interactive/composite widget
3. Check references/accessible-css.md when writing styles for focus, motion, color, or layout
4. Check references/modern-apis.md before reaching for a JS API you're unsure is current
5. Write the code
6. Self-check against the checklist below before returning code

## Pre-return checklist

- [ ] Headings are hierarchical, no skipped levels
- [ ] Every interactive element is a real interactive element or has role+keyboard handling
- [ ] Images have appropriate alt text (or `alt=""` if decorative)
- [ ] Forms have associated `<label>`s, and errors are announced (`aria-live`/`aria-describedby`)
- [ ] Focus is visible (`:focus-visible`), never suppressed without a replacement
- [ ] Color/contrast isn't the only signal for state or meaning; contrast meets WCAG AA
- [ ] Motion/animation respects `prefers-reduced-motion`
- [ ] Focus order is logical; focus is managed on dynamic changes (e.g., modal open)
- [ ] No deprecated or non-standard APIs used without a stated reason

## When to consult reference files

- Building a custom interactive widget (menu, combobox, tabs, dialog, accordion)? → `references/aria-patterns.md`
- Writing styles for focus, motion, color scheme, or responsive layout? → `references/accessible-css.md`
- Reaching for a browser API and unsure if it's current best practice? → `references/modern-apis.md`
