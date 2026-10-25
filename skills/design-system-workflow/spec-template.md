# Spec: <feature or screen name>

## Summary
<2-3 sentences: what changes and why>

## Chosen idea
- **Source:** <idea name from ideas.md>
- **System fit:** fits / stretches / breaks
- **Scores:** value <n>, effort <n>, consistency <n>, risk <n>

## Component list
| Component | Status | Path | Notes |
|-----------|--------|------|-------|
| <Button> | Reuse | src/components/Button | variant: primary |
| <NewCard> | New (Extend) | src/components/NewCard | see gap #1 |

## Token diff

### Added
| Token | Value | Usage |
|-------|-------|-------|
| `<token-name>` | `<value>` | <where it is used> |

### Changed
| Token | Old | New | Reason |
|-------|-----|-----|--------|

### Removed
| Token | Replacement |
|-------|-------------|

## Gap log summary
- Adapt: <n>
- Extend: <n>
- Exception: <n>
Full log: `gap-log.md`

## Accessibility
- Contrast: <result>
- Keyboard and focus: <result>
- Touch targets: <result>

## Implementation steps
1. <step>
2. <step>

## Drift review (after shipping)
- [ ] No hard-coded values that should be tokens
- [ ] No duplicate components
- [ ] All exceptions documented with owner and review date
- [ ] Accepted extensions added to the system docs
