# Adapter: bootstrap

Covers Bootstrap 5 customized through Sass variables, with `--bs-*` CSS variables as the runtime layer. Bootstrap 4 uses the same Sass approach but has no `--bs-*` variables for most tokens.

## Detect
- `package.json` has `bootstrap` (and optionally `sass`, `react-bootstrap`, `reactstrap`).
- A Sass file imports Bootstrap: `@import "bootstrap/scss/bootstrap";` or `@use "bootstrap/scss/bootstrap" with (...)`.
- A custom variables file such as `_variables.scss`, `custom.scss`, or `theme.scss`.
- If the project only loads Bootstrap from a CDN and overrides with CSS, use the `css-variables` adapter for the overrides.

## Read
1. **Project tokens are the overrides.** Read the custom Sass file(s) that set variables before the Bootstrap import (or in `@use ... with (...)`).
2. Bootstrap's own defaults (`$primary: #0d6efd`, `$spacer: 1rem`, and so on) are **framework defaults**, not project tokens. Only list defaults the user asks for.
3. Map Sass variables to categories:

| Sass variable | category |
|---------------|----------|
| `$primary`, `$secondary`, `$success`, `$info`, `$warning`, `$danger`, `$light`, `$dark`, `$body-bg`, `$body-color`, `$link-color` | color |
| `$theme-colors` map | color (one token per key) |
| `$blue`, `$indigo`, ... `$gray-100`...`$gray-900` | color (base palette) |
| `$spacer`, `$spacers` map | spacing |
| `$border-radius`, `$border-radius-sm/lg/xl/pill` | radius |
| `$border-width`, `$border-color` | border |
| `$font-family-base`, `$font-family-monospace` | font-family |
| `$font-size-base`, `$h1-font-size` ... | font-size |
| `$font-weight-*` | font-weight |
| `$line-height-base` | line-height |
| `$box-shadow`, `$box-shadow-sm/lg` | shadow |
| `$grid-breakpoints` map | breakpoint |
| `$zindex-*` | z-index |
| `$transition-base`, `$transition-fade` | motion |
| `$enable-*` (rounded, shadows, etc.) | feature flag (note, not a token) |

4. Resolve Sass: variables referencing other variables (`$link-color: $primary`), functions (`darken()`, `tint-color()`, `shade-color()`), and math. If a function cannot be evaluated by reading, keep `raw` and mark the value as unresolved. If `sass` is installed, you may compile a small file to get final values.
5. Map entries (`$theme-colors`, `$spacers`, `$grid-breakpoints`) merge with defaults using `map-merge`. Each key you add or change is a token.
6. **Runtime layer:** Bootstrap 5.3 exposes `--bs-primary`, `--bs-body-bg`, `--bs-border-radius`, etc. Overrides of `--bs-*` in CSS are tokens too; read them with the `css-variables` adapter.
7. Color modes: `data-bs-theme="dark"` rules and `$color-mode-type` settings give `mode: dark` tokens.

## Components
- With plain Bootstrap, "components" are Bootstrap classes (`btn`, `card`, `navbar`, `modal`) plus any custom partials. Report which Bootstrap components the app uses and any custom SCSS partials that extend them.
- With `react-bootstrap` or `reactstrap`, list wrapper components in `components_path` and which Bootstrap components they use.
- Inline styles or custom CSS that bypass variables are drift. List them separately.

## Write
Propose Sass lines placed **before** the Bootstrap import (variables must be defined before they are used).

```scss
// _variables.scss  (imported before bootstrap)

// Added / Changed
$primary: #2563eb;            // was #0d6efd
$border-radius: 0.75rem;      // was 0.375rem

// Map additions
$theme-colors: map-merge(
  (
    "accent": #7c3aed,        // Added
  ),
  $theme-colors
);

@import "bootstrap/scss/bootstrap";
```

With `@use`:
```scss
@use "bootstrap/scss/bootstrap" with (
  $primary: #2563eb,
  $border-radius: 0.75rem
);
```

Runtime-only changes (no rebuild): propose `--bs-*` overrides in CSS.
```css
:root { --bs-primary: #2563eb; --bs-border-radius: 0.75rem; }
```

For a removed token, delete the override so the Bootstrap default returns, and say what the default value is.

## Pitfalls
- Order matters: variables set after the Bootstrap import have no effect.
- Changing `$primary` does not always update derived values (`$primary-rgb`, `--bs-primary-rgb`, link hover colors). Check the derived tokens.
- Bootstrap 5.3 builds `--bs-*` variables at compile time. Changing a Sass variable regenerates them; a CSS override only changes the runtime value.
- Utility classes (`text-primary`, `bg-light`) follow `$theme-colors`; adding a map key also generates new utilities.
- Do not treat Bootstrap's default palette as the project's design system.

## Test case

Input:
```scss
$primary: #2563eb;
$link-color: $primary;
$border-radius: 0.75rem;
$spacer: 1.25rem;
@import "bootstrap/scss/bootstrap";
```
Expected:

| category | name | value | raw | alias_of |
|----------|------|-------|-----|----------|
| color | primary | #2563eb | | |
| color | link-color | #2563eb | $primary | primary |
| radius | border-radius | 0.75rem | | |
| spacing | spacer | 1.25rem | | |
