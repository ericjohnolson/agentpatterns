# Research Artifact Template

## Structure

```markdown
# {Feature Name} - Research

**Date:** YYYY-MM-DD
**Status:** Research Complete

## Summary

2-3 sentence overview of what was discovered.

## Relevant Files

### Core Layer (Pure Functions)
- `src/core/{module}/{file}.ts` - What it does, how it's relevant

### Features Layer (Use Cases)
- `src/features/{domain}/{file}.ts` - What it does, how it's relevant

### Shell Layer (I/O)
- `src/shell/database/repositories/{file}.ts` - What it does
- `src/shell/http/routes/{file}.ts` - What it does

### Tests
- `tests/unit/core/{module}/{file}.test.ts` - Test patterns to follow
- `tests/integration/features/{file}.test.ts` - Integration patterns

## Existing Patterns

### Pattern 1: {Name}
**Used in:** {files}
**How it works:** {brief description}
**Applicable to new feature:** {yes/no and why}

### Pattern 2: {Name}
...

## Architectural Boundaries Affected

### L3 Boundaries (Core/Features)
- {Which core functions will be needed}
- {Which feature use cases will change}

### L4 Boundaries (HTTP/External)
- {Which HTTP routes will be added/modified}
- {Which external integrations are involved}

## Database Schema

### Existing Tables
- `{table_name}` - {relevant fields}

### Schema Changes Needed
- {table modifications or new tables}

## Integration Points

### Internal
- {Which features this integrates with}
- {Shared core functions}

### External
- {Square API integration points}
- {Other external services}

## Constraints & Considerations

- {Technical constraints discovered}
- {Architectural constraints}
- {Performance considerations}
- {Security considerations}

## Open Questions

- {Question 1 for user clarification}
- {Question 2 for user clarification}

## Next Steps

Hand off to `/craft-plan` with this research artifact.
```

## Example Research Scenario

When user says:
> "I need to add discount codes to order creation"

Your research should explore:
1. **Existing discount logic** - Are there discounts already? Where?
2. **Order creation flow** - How are orders created today?
3. **Validation patterns** - How are other codes/slugs validated?
4. **Database schema** - Where would discount codes be stored?
5. **HTTP routes** - Which endpoints need modification?
6. **Test patterns** - How are similar features tested?

## Quality Standards

### Completeness
- [ ] All relevant files identified and documented
- [ ] Existing patterns analyzed for applicability
- [ ] Architectural boundaries clearly mapped
- [ ] Database schema impact understood
- [ ] Integration points identified
- [ ] Constraints and considerations listed

### Conciseness
- [ ] Research artifact is ~200 lines or less
- [ ] Only relevant details included
- [ ] No unnecessary code snippets (file paths and brief descriptions only)
- [ ] Clear and scannable structure

### Actionability
- [ ] Findings directly inform planning phase
- [ ] Open questions clearly stated
- [ ] Next steps clearly defined
