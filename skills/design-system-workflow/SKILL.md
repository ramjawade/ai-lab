---
name: design-system-workflow
description: Runs a two-lane design workflow on an existing app - explore freely outside the design system, then convert the winning ideas back into the system. Use when the user wants to redesign a screen or flow, audit design tokens or components, brainstorm "out of the box" UI ideas, or turn a design idea into a spec that fits their design system. Produces a gap log, token diff, and implementation spec.
---

# Design System Workflow

Two lanes: **diverge** (ignore the system on purpose) and **converge** (map back into the system). Wild ideas are never lost, and the system is never polluted by one-offs. The **gap log** is what connects the two lanes.

## Before you start

1. Look for `design-system.config.md` in the repo root. If it exists, read it: it points to the adapter, token files, the component folder, and constraints.
2. If it does not exist, ask the user for: framework, where tokens live, where components live, and non-negotiable constraints. Then offer to create the config from `design-system.config.md` (the example in this skill).
3. **Pick the adapter.** Read `adapters/README.md`, then the adapter named in the config's `adapter` field (`tailwind`, `bootstrap`, `css-variables`). If it is `auto` or missing, follow the detection order in `adapters/README.md` and tell the user which adapter you chose. If nothing matches, use `css-variables` and ask the user to confirm where tokens live.
4. Ask which phase the user wants to run. Default to Phase 1, then continue in order. The user may run phases separately.

## Working with adapters

- All phases work in the **common token model** (see `adapters/README.md`). Never mix framework syntax into ideas, scores, or the gap log.
- Phase 1 uses the adapter's **Read** and **Components** sections to inventory tokens and components.
- Phase 4 uses the adapter's **Write** section to express the token diff in the framework's native format.
- Framework defaults (Tailwind's default palette, Bootstrap's default variables) are not project tokens. Do not list them unless the user asks.
- A project can use more than one adapter (e.g. Tailwind plus CSS variables). Read each one that applies and note which source each token came from.
- If the user's setup does not fit any adapter, use `css-variables` plus the `overrides` field in the config, and suggest adding a new adapter (see `adapters/README.md`).

## Phases

### Phase 1: Audit (anchor in what exists)
- Inventory tokens with the adapter's **Read** steps: color, type, spacing, radius, elevation, motion. Record each in the common token model, with its source file.
- Inventory components with the adapter's **Components** steps: name, file path, variants, where used. List hard-coded values (drift) separately.
- Record fixed constraints: framework, accessibility level, brand rules, data density, browser/device support.
- Output: a short audit summary. Do not invent tokens or components. If something cannot be found, say so.

### Phase 2: Diverge (out of the system, on purpose)
- Brainstorm layouts, interactions, and visual ideas for the target screen or flow with no system constraints.
- Produce 2-3 options per screen. At least one must deliberately break the system.
- Tag each idea: **fits**, **stretches**, or **breaks** the current system.
- Do not filter or soften ideas in this phase. Filtering happens in Phase 3.

### Phase 3: Evaluate
- Score each idea 1-5 on: user value, build effort (5 = easy), consistency cost (5 = low cost), risk (5 = low risk).
- Show the scores in a table with a one-line reason per idea.
- Recommend winners, but let the user decide. Park the rest in a "Future ideas" list.

### Phase 4: Convert (back into the system)
- Map each winning idea to existing tokens and components, 1:1 where possible.
- For anything that does not map, make a **gap decision** and log it with `gap-log-template.md`:
  - **Adapt**: rework the idea to fit the system.
  - **Extend**: propose a new token or component.
  - **Exception**: ship as a documented one-off, with a reason and an owner.
- Output a spec using `spec-template.md`: component list, token diff, and gap log. Show the token diff twice: once in the common model, and once in the framework's native format using the adapter's **Write** section.

### Phase 5: Ship and learn
- Help implement against the real code. Prefer existing components and tokens.
- After implementation, review for drift: hard-coded values, duplicated components, undocumented exceptions.
- Suggest which accepted **Extend** items should be promoted into the system, and which **Exceptions** should be revisited.

## Rules

- Never claim a token or component exists without finding it in the repo or config.
- Never edit the real token files or framework config until the user approves the spec. Adapter output is a proposed diff.
- Keep the diverge lane and the convert lane visibly separate in outputs.
- Every gap must have a decision (Adapt, Extend, or Exception). No gap is left open.
- Prefer Adapt over Extend, and Extend over Exception.
- Check accessibility (contrast, focus states, touch targets) on every converted idea.
- Do not change files in the repo until the user approves the spec.

## Outputs

Write outputs next to the work, e.g. `docs/design/<feature>/`:
- `audit.md` (Phase 1)
- `ideas.md` (Phases 2-3)
- `gap-log.md` (Phase 4, from `gap-log-template.md`)
- `spec.md` (Phase 4, from `spec-template.md`)
