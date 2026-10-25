# Adapters

An adapter teaches the skill how one framework defines its design system. The workflow itself (Phases 1-5) always works in the **common token model** below, so it never needs to know the framework.

## Common token model

Every token found in a repo is recorded like this:

| Field | Meaning | Example |
|-------|---------|---------|
| `category` | color, font-family, font-size, font-weight, line-height, spacing, radius, shadow, border, breakpoint, z-index, motion, opacity | `color` |
| `name` | Name as written in the source | `primary` |
| `value` | Resolved value (follow aliases to the end) | `#2563EB` |
| `raw` | Value as written, if different (alias, function, calc) | `$blue-600` |
| `source` | File and line where it is defined | `src/styles/_variables.scss:12` |
| `alias_of` | Token it points to, if any | `blue-600` |
| `mode` | light, dark, or default | `default` |

Audit output (Phase 1) lists tokens in this model. The token diff (spec) lists changes in this model, then re-expresses each one in the framework's native format using the adapter's **Write** section.

## Adapter contract

Each adapter file must have these sections:

1. **Detect:** how to recognize the framework in a repo.
2. **Read:** where tokens live and how to map them to the common model.
3. **Components:** where components live and how to list them.
4. **Write:** how to express added, changed, and removed tokens natively.
5. **Pitfalls:** things that commonly go wrong.
6. **Test case:** a small sample input and the expected common-model output.

## Rules for every adapter

- Read-only until the user approves the spec. Write output is a proposed diff, not an edit.
- Resolve aliases and functions to a final value when possible. If not possible, keep `raw` and say the value is unresolved.
- Never invent tokens. If the framework defaults apply (e.g. Tailwind's default palette), say "framework default" and do not list them as project tokens unless the user asks.
- Record every source file read, so the user can verify.

## Available adapters

| Adapter | File | Use for |
|---------|------|---------|
| `css-variables` | `css-variables.md` | Universal fallback; any project with `:root { --x }` |
| `tailwind` | `tailwind.md` | Tailwind v3 (config file) and v4 (`@theme` in CSS) |
| `bootstrap` | `bootstrap.md` | Bootstrap 5 via Sass variables and `--bs-*` CSS variables |

## Detection order (when `adapter: auto`)

1. `package.json` dependencies: `tailwindcss` (check version for v3 vs v4), `bootstrap`.
2. Files: `tailwind.config.*`, `@import "tailwindcss"` or `@theme` in a CSS file, `_variables.scss` that imports bootstrap.
3. If more than one matches, prefer the one the components actually use; ask the user if unclear.
4. If none match, use `css-variables` and ask the user to confirm token locations.

## Adding a new adapter

Copy the structure above, add a row to the table, and add a test case. Planned next: `mui`, `style-dictionary` (W3C DTCG JSON), `chakra`, `shadcn`.
