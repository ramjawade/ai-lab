# Design System Config

Copy this file to the repo root and fill it in. The design-system-workflow skill reads it first.

## Adapter
- adapter: auto   <!-- auto | tailwind | bootstrap | css-variables. `auto` detects from package.json and config files. -->
- adapter_version: <!-- optional, e.g. tailwind 4, bootstrap 5.3 -->

## Where things live
- token_sources:   <!-- files that define tokens; the adapter tells you what to put here -->
  - src/styles/tokens.css
- components_path: src/components/
- storybook: npm run storybook   <!-- optional -->
- design_source: Figma (link here)   <!-- optional -->

## Stack
- Framework: React + TypeScript
- Styling: Tailwind CSS   <!-- or Bootstrap, MUI, CSS variables, styled-components, etc. -->

## Adapter examples (keep only the one you use)

### Tailwind v3
- adapter: tailwind
- token_sources:
  - tailwind.config.ts
  - src/index.css   <!-- if tokens are also CSS variables -->

### Tailwind v4
- adapter: tailwind
- token_sources:
  - src/app.css   <!-- file with @theme -->

### Bootstrap 5
- adapter: bootstrap
- token_sources:
  - src/scss/_variables.scss   <!-- Sass overrides set before the Bootstrap import -->
  - src/scss/custom.scss

### Plain CSS variables or anything else
- adapter: css-variables
- token_sources:
  - src/styles/tokens.css

## Overrides (only for custom setups)
<!-- Tell the skill how your project differs from the adapter's defaults. Examples: -->
<!-- - Token prefix: all our CSS variables start with `--acme-` -->
<!-- - Category hints: `--acme-gap-*` are spacing tokens -->
<!-- - Ignore: do not treat `src/vendor/**` as project tokens -->

## Fixed constraints
- Accessibility: WCAG 2.2 AA
- Brand: primary color and logo rules are not negotiable
- Density: data-heavy screens, compact spacing preferred
- Browsers/devices: latest 2 versions of evergreen browsers, mobile web supported

## Conventions
- Token naming: `category-role-variant` (e.g. `color-text-muted`)
- Exceptions require: reason, owner, review date
- New components require: Storybook story and a11y check
