# Design System Config

Copy this file to the repo root and fill it in. The design-system-workflow skill reads it first.

## Stack
- Framework: React + TypeScript
- Styling: Tailwind CSS   <!-- or MUI, CSS variables, styled-components, etc. -->
- Design source: Figma (link here)  <!-- optional -->

## Where things live
- Tokens: `src/styles/tokens.css`  <!-- or tailwind.config.ts, theme.ts -->
- Components: `src/components/`
- Storybook: `npm run storybook`  <!-- optional -->

## Fixed constraints
- Accessibility: WCAG 2.2 AA
- Brand: primary color and logo rules are not negotiable
- Density: data-heavy screens, compact spacing preferred
- Browsers/devices: latest 2 versions of evergreen browsers, mobile web supported

## Conventions
- Token naming: `category-role-variant` (e.g. `color-text-muted`)
- Exceptions require: reason, owner, review date
- New components require: Storybook story and a11y check
