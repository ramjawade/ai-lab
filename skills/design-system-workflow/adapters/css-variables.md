# Adapter: css-variables

Universal fallback. Works for any project that defines tokens as CSS custom properties, and for any framework not covered by another adapter.

## Detect
- Files containing `:root {` or `[data-theme=...] {` with `--name: value;` lines.
- Common paths: `src/styles/tokens.css`, `src/index.css`, `src/styles/variables.css`, `styles/globals.css`.
- Search for `--` declarations: `grep -rn "^\s*--[a-z]" --include=*.css --include=*.scss`.

## Read
1. Collect every custom property declared in `:root`. Each one is a token.
2. Infer `category` from the name prefix or value:
   - `--color-*`, `--bg-*`, values like `#hex`, `rgb()`, `hsl()` → `color`
   - `--space-*`, `--spacing-*`, `--gap-*` → `spacing`
   - `--radius-*`, `--rounded-*` → `radius`
   - `--font-*`, `--text-*` → `font-family` or `font-size` (by value)
   - `--shadow-*`, `--elevation-*` → `shadow`
   - `--duration-*`, `--ease-*`, `--transition-*` → `motion`
3. Resolve `var(--other)` chains: set `alias_of` to the referenced token and `value` to the final value.
4. Dark mode: tokens redeclared under `.dark`, `[data-theme="dark"]`, or `@media (prefers-color-scheme: dark)` are the same token with `mode: dark`.
5. Unknown prefix: set `category` to `unknown` and list it in the audit for the user to classify.

## Components
- Use `components_path` from the config. List each component file, its exported name, and which tokens it references (`var(--x)`).
- Hard-coded values (hex, px) inside components are drift. List them separately.

## Write
Express the token diff as CSS lines in the file where tokens already live.

```css
/* Added */
:root {
  --color-accent-subtle: #eef2ff;
}

/* Changed */
:root {
  --space-md: 1.25rem; /* was 1rem */
}

/* Removed: delete the line, and replace usages with the replacement token */
```

For dark mode, add the matching line inside the existing dark selector.

## Pitfalls
- Tokens defined on `body` or a component class are scoped, not global. Note the scope.
- `calc()` values cannot always be resolved. Keep `raw` and mark unresolved.
- Do not treat third-party CSS variables (e.g. from a library's stylesheet) as project tokens unless the user says so.

## Test case

Input:
```css
:root {
  --color-brand: #2563eb;
  --color-link: var(--color-brand);
  --space-md: 1rem;
}
.dark { --color-brand: #60a5fa; }
```

Expected:

| category | name | value | raw | alias_of | mode |
|----------|------|-------|-----|----------|------|
| color | color-brand | #2563eb | | | default |
| color | color-brand | #60a5fa | | | dark |
| color | color-link | #2563eb | var(--color-brand) | color-brand | default |
| spacing | space-md | 1rem | | | default |
