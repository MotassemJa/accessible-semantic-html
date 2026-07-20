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
| `command`/`commandfor` attributes | `popovertarget`/`popovertargetaction` + JS click handlers for opening/closing popovers and dialogs |
| Navigation API (`navigation.addEventListener('navigate', ...)`) | Manual `pushState()` + `popstate` wiring for SPA routing |
| `URL` / `URLSearchParams` | Manual query-string parsing |
| `navigator.clipboard.writeText()` | `document.execCommand('copy')` |
| `Array.prototype.at()`, `.flatMap()`, `.group()` (where supported) | Manual index math / lodash for simple cases |
| `crypto.randomUUID()` | Custom ID-generation hacks |
| `element.showPopover()` / Popover API | z-index + click-outside-listener stacks |
| CSS `:has()` | JS-based parent-selection workarounds |
| `requestIdleCallback` | Arbitrary `setTimeout(0)` for deferring non-urgent work |

## Declarative popover/dialog control: `command`/`commandfor`

For buttons that open/close a `<dialog>` or toggle a `popover` element, prefer the
`command`/`commandfor` attributes over a click listener that calls `.showModal()`,
`.close()`, or `.togglePopover()` — the browser wires up the interaction (and its
accessibility semantics) declaratively:

```html
<button commandfor="mypopover" command="toggle-popover">Toggle</button>
<div id="mypopover" popover>
  <button commandfor="mypopover" command="hide-popover">Close</button>
</div>

<button commandfor="mydialog" command="show-modal">Open dialog</button>
<dialog id="mydialog">
  <button commandfor="mydialog" command="close">Close</button>
</dialog>
```

Built-in commands: `show-modal`, `close` (dialogs); `show-popover`, `hide-popover`,
`toggle-popover` (popovers). For custom widget behavior, use a `--`-prefixed custom
command name (e.g. `command="--rotate-left"`) and listen for the `command` event on
the target element — this still gets you the declarative HTML wiring without needing
a built-in action to match.

## Navigation API for SPA routing

For client-side routing, prefer the Navigation API over manually combining
`click` listeners, `history.pushState()`, and `popstate`:

```javascript
navigation.addEventListener('navigate', (event) => {
  if (!event.canIntercept) return;
  event.intercept({
    async handler() {
      // fetch data, update the DOM for event.destination.url
    }
  });
});
```

One listener handles link clicks, form submissions, and back/forward navigation
consistently, the URL updates automatically, and `event.scroll()` gives explicit
control over when scroll-position restoration happens (e.g. after content renders).
Use `event.destination.url` to read the target URL and `event.formData` to read
submitted form data for form-based navigations.

## Forms

- Use built-in HTML validation (`required`, `pattern`, `type="email"`, etc.) as the
  first layer, with JS validation layered on top for UX — not as a replacement.
- `ElementInternals`/form-associated custom elements when building custom form controls
  as Web Components, so they participate in native form submission and validation.

## A note on caution

Always sanity-check that a given API is appropriate for the project's actual browser
support target — don't assume "modern" is always right without knowing the constraint.
If unsure, note the assumption ("assuming evergreen browser support") in the response.
