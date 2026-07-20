# Modern Web Platform APIs — Defaults to Prefer

Widely supported (Baseline "widely available") APIs and elements to default to,
and what they replace. Check current support if a project needs to support very
old browsers, but assume evergreen browsers unless told otherwise.

## HTML elements

| Use this | Instead of |
|---|---|
| `<dialog>` | Custom modal div + JS focus trap library |
| `<details>`/`<summary>` | JS-toggled disclosure widgets for simple cases |
| `<datalist>` | Custom autocomplete for simple suggestion lists |
| `<search>` | Generic `<div role="search">` wrapper |
| `loading="lazy"` on `<img>`/`<iframe>` | JS-based lazy-load libraries |
| `<input type="date/color/range/...">` | Custom-built pickers for standard inputs |

## JavaScript APIs

| Use this | Instead of |
|---|---|
| `fetch` + `async`/`await` | `XMLHttpRequest`, callback-based AJAX |
| `structuredClone()` | `JSON.parse(JSON.stringify(x))` for deep cloning |
| `IntersectionObserver` | Scroll event listeners for visibility detection |
| `ResizeObserver` | Polling `getBoundingClientRect` on resize |
| `popover` attribute / Popover API | Custom-positioned dropdown/tooltip div stacks (for non-modal popovers) |
| `<dialog>.showModal()` / `close()` | Manual `aria-hidden` toggling + focus trap for modals |
| `URL` / `URLSearchParams` | Manual query-string parsing |
| `navigator.clipboard.writeText()` | `document.execCommand('copy')` |
| `Array.prototype.at()`, `.flatMap()`, `.group()` (where supported) | Manual index math / lodash for simple cases |
| `crypto.randomUUID()` | Custom ID-generation hacks |
| `element.showPopover()` / Popover API | z-index + click-outside-listener stacks |
| CSS `:has()` | JS-based parent-selection workarounds |
| `requestIdleCallback` | Arbitrary `setTimeout(0)` for deferring non-urgent work |

## Forms

- Use built-in HTML validation (`required`, `pattern`, `type="email"`, etc.) as the
  first layer, with JS validation layered on top for UX — not as a replacement.
- `ElementInternals`/form-associated custom elements when building custom form controls
  as Web Components, so they participate in native form submission and validation.

## A note on caution

Always sanity-check that a given API is appropriate for the project's actual browser
support target — don't assume "modern" is always right without knowing the constraint.
If unsure, note the assumption ("assuming evergreen browser support") in the response.
