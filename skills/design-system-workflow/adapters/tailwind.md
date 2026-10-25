# Adapter: tailwind

Covers Tailwind CSS v3 (JavaScript/TypeScript config) and v4 (CSS-first `@theme`).

## Detect
- `package.json` has `tailwindcss`. Read its version: `3.x` → v3 mode, `4.x` → v4 mode.
- v3: `tailwind.config.js`, `.ts`, `.cjs`, or `.mjs` exists.
- v4: a CSS file has `@import "tailwindcss"` and/or an `@theme` block. A `tailwind.config.*` may still exist via `@config`; if so, read both.
- Monorepos: check each package that has its own Tailwind config.

## Read

### v3
1. Open the config. Tokens come from `theme` (replaces defaults) and `theme.extend` (adds to defaults).
2. Map keys to categories:

| Config key | category |
|------------|----------|
| `colors` | color |
| `spacing`, `margin`, `padding`, `gap` | spacing |
| `borderRadius` | radius |
| `fontSize` | font-size |
| `fontFamily` | font-family |
| `fontWeight` | font-weight |
| `lineHeight` | line-height |
| `boxShadow` | shadow |
| `screens` | breakpoint |
| `zIndex` | z-index |
| `transitionDuration`, `transitionTimingFunction` | motion |

3. Nested colors flatten with a dash: `colors: { brand: { 500: "#2563eb" } }` → `brand-500`.
4. A value of `"var(--x)"` is an alias to a CSS variable. Follow it using the `css-variables` adapter.
5. `theme.extend` values are project tokens. If `theme` (not `extend`) overrides a key, the defaults for that key are gone; say so.
6. Plugins and `presets` can add tokens. Read them if local; note them if from `node_modules`.

### v4
1. Read each `@theme { ... }` block. Variables use namespaces that decide the category:

| Variable prefix | category |
|-----------------|----------|
| `--color-*` | color |
| `--spacing` (base unit) | spacing |
| `--radius-*` | radius |
| `--font-*` | font-family |
| `--text-*` | font-size |
| `--font-weight-*` | font-weight |
| `--leading-*` | line-height |
| `--shadow-*` | shadow |
| `--breakpoint-*` | breakpoint |
| `--ease-*`, `--animate-*` | motion |

2. In v4, `--spacing` is one base value (e.g. `0.25rem`); spacing utilities are multiples of it. Record the base, not every multiple.
3. Variables outside `@theme` (plain `:root`) are normal CSS variables; use the `css-variables` adapter for them.
4. `@theme inline` and `@theme static` change how variables are emitted; the tokens are the same.

### Both versions
- Dark mode: `dark:` variants in classes, `darkMode` setting (v3), or `@variant dark` (v4). Tokens redefined under a dark selector use `mode: dark`.
- Tailwind's default palette and scale are **framework defaults**, not project tokens. Do not list them unless the user asks.

## Components
- Components are usually files using class strings. Use `components_path` from the config.
- Report for each component: file, exported name, and which token-based classes it uses (e.g. `bg-brand-500`, `rounded-card`).
- Arbitrary values like `bg-[#123456]` or `p-[13px]` are drift. List them separately.
- If the project uses `cva`, `tailwind-variants`, or shadcn/ui, read the variants from there.

## Write

### v3: propose entries inside `theme.extend`
```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        brand: { 500: "#2563eb" },        // Added
      },
      borderRadius: {
        card: "0.75rem",                  // Added
      },
    },
  },
};
```
Changed tokens: show old and new in a comment. Removed tokens: delete the key and list where its classes are still used.

### v4: propose lines inside the existing `@theme` block
```css
@theme {
  --color-brand-500: #2563eb;   /* Added */
  --radius-card: 0.75rem;       /* Added */
}
```

If the project defines tokens as CSS variables and maps them in Tailwind (`colors: { brand: "var(--brand)" }`), add the variable with the `css-variables` adapter first, then the Tailwind mapping.

## Pitfalls
- Overriding `theme.colors` (not `extend`) silently removes all default colors.
- Class strings built dynamically (`bg-${color}-500`) are not detected by Tailwind or by a text search. Flag them.
- v3 → v4 migrations leave both a config file and `@theme`. Check which one is actually loaded.
- Opacity modifiers (`bg-brand-500/50`) are not separate tokens.

## Test case

Input (v3):
```js
module.exports = {
  theme: {
    extend: {
      colors: { brand: { 500: "#2563eb" }, surface: "var(--surface)" },
      borderRadius: { card: "0.75rem" },
    },
  },
};
```
Expected:

| category | name | value | raw | alias_of |
|----------|------|-------|-----|----------|
| color | brand-500 | #2563eb | | |
| color | surface | (resolve from CSS) | var(--surface) | surface (css var) |
| radius | card | 0.75rem | | |

Input (v4):
```css
@import "tailwindcss";
@theme { --color-brand-500: #2563eb; --radius-card: 0.75rem; }
```
Expected: the same `brand-500` and `card` rows.
