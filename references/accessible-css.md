# Accessible & Modern CSS Reference

## Design tokens & custom properties

- Prefer CSS custom properties (`--color-error`, `--space-md`, `--radius-sm`, etc.)
  over hardcoded color/spacing/radius/font values sprinkled through selectors —
  define them once (`:root` or a theme scope) and reference them with `var()`.
- This isn't just tidiness: it's what makes `prefers-color-scheme`/`light-dark()`
  theming, user-customizable themes, and consistent focus/contrast values
  maintainable in the first place — a hardcoded `#111` can't respond to a theme
  or contrast preference change the way `var(--color-text)` can.
- If the project already has a design token system (Style Dictionary output, a
  design-system package, Tailwind theme values, etc.), use those tokens instead
  of introducing parallel ad hoc values.

## Cascade & specificity

- Use `@layer` to control cascade order instead of reaching for `!important` or
  piling on extra selectors to win specificity fights:
  ```css
  @layer reset, base, components, utilities;

  @layer base {
    button { color: var(--color-text); }
  }
  @layer components {
    .btn-primary { color: var(--color-on-accent); }
  }
  ```
  Layers declared earlier lose to layers declared later regardless of selector
  specificity within them, so ordering the layers (not stacking classes/IDs) is
  what decides precedence.
- Avoid `!important` — it breaks the normal cascade for everyone downstream (including
  assistive-tech-relevant overrides like a consumer's focus-visible or contrast
  styles) and is a sign the underlying specificity/layer structure needs fixing,
  not patching over.
- Avoid complex/deeply-nested selectors (`.a .b > .c + .d span`) for the same
  reason — they're both hard to override intentionally later and easy to
  accidentally override. Prefer a single class per styled concern.
- Avoid overriding another component/library's styles when possible — if a shared
  component doesn't support the variant you need, extend it via its documented
  API (props, CSS custom property hooks, slots) rather than fighting its CSS from
  the outside.

## Focus states

- Use `:focus-visible` instead of suppressing `:focus` outlines or showing them on
  every mouse click. It shows focus rings for keyboard users without cluttering
  mouse/touch interaction.
  ```css
  button:focus-visible {
    outline: 2px solid currentColor;
    outline-offset: 2px;
  }
  ```
- Never set `outline: none` without providing a clearly visible replacement.

## Motion preferences

- Wrap non-essential animation/transition in a `prefers-reduced-motion` check:
  ```css
  @media (prefers-reduced-motion: no-preference) {
    .card { transition: transform 0.2s ease; }
  }
  ```
  or the inverse — define the reduced version explicitly:
  ```css
  @media (prefers-reduced-motion: reduce) {
    * { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; }
  }
  ```

## Color scheme & contrast

- Support `prefers-color-scheme` (light/dark) rather than assuming one theme.
- Use `light-dark()` (modern, widely supported) to simplify theming:
  ```css
  :root { color-scheme: light dark; }
  body { background: light-dark(#fff, #111); color: light-dark(#111, #eee); }
  ```
- Check `prefers-contrast: more` for users who need higher contrast; don't rely on
  color alone to convey state — pair with icon/text/pattern.
- Target WCAG AA contrast ratios: 4.5:1 for normal text, 3:1 for large text/UI components.
- For text color against a dynamic/user-generated background (theme pickers, tags,
  avatars), use `contrast-color()` instead of a hand-picked or JS-computed black/white:
  ```css
  .tag { background: var(--tag-color); color: contrast-color(var(--tag-color)); }
  ```
  It picks whichever of black/white contrasts best against the given color. Baseline
  newly available (Chrome/Edge/Firefox 146+, Safari 26+) — provide a `color: #000`
  fallback declaration before it for older browsers, since `@supports` on a color
  function value isn't reliable.

## Form validation styling

- Style validity with `:user-valid`/`:user-invalid` instead of `:valid`/`:invalid`
  for feedback shown as the user types. `:invalid` matches an empty required field
  immediately on page load; `:user-valid`/`:user-invalid` only match after the user
  has meaningfully interacted with the field, so errors don't appear before they've
  had a chance to fill anything in.
  ```css
  input:user-invalid { border-color: var(--color-error); }
  input:user-valid { border-color: var(--color-success); }
  ```
- This replaces manually tracking "has this field been touched/changed" in JS with
  a `blur`/`input` listener and a class toggle — the browser tracks it natively.

## Layout

- Prefer `grid`/`flexbox` with logical properties (`margin-inline`, `padding-block`)
  over legacy float/positioning hacks — these also adapt better to RTL languages.
- Use `gap` instead of margin-based spacing hacks in flex/grid containers.
- Use container queries (`@container`) for component-level responsiveness where
  appropriate, rather than only viewport-based media queries.
- For elements that need to be positioned relative to a trigger (tooltips, popover
  menus, autocomplete suggestion lists, context menus), prefer
  [CSS anchor positioning](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Anchor_positioning)
  over a JS positioning library (Floating UI/Popper) or manual `getBoundingClientRect()`
  math:
  ```css
  .trigger { anchor-name: --my-anchor; }
  .tooltip {
    position: fixed;
    position-anchor: --my-anchor;
    position-area: bottom center;
    position-try-fallbacks: top center, left center, right center;
    position-visibility: auto; /* hide rather than render off-screen/clipped */
  }
  ```
  Combine with `position-try-fallbacks` so the browser flips the element to a side
  that still fits the viewport instead of letting it render cut off, and pair it
  with the Popover API/`popover` attribute (see references/aria-patterns.md) for
  the open/close and light-dismiss behavior. This is a newer feature — verify
  current browser support before relying on it without a fallback.

## Text & readability

- Avoid `font-size` in fixed px for body text; use `rem` so it respects user
  browser zoom/font-size settings.
- Set a reasonable `max-width` (e.g. `65ch`) on long text blocks for readability.
- Ensure line-height is at least 1.5 for body text (WCAG 1.4.12).

## Interactive element sizing

- Touch targets should be at least 24x24px (WCAG 2.2 AA), ideally 44x44px, with
  adequate spacing between adjacent targets.

## Things to avoid

- `user-select: none` on primary content text.
- Removing `resize` on form textareas without reason.
- Fixed viewport zoom disabling (`user-scalable=no` in viewport meta) — never do this.
