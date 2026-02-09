---
name: craft-research
description: Research phase of RPI methodology. Spawns parallel subagents to explore codebase and produces compact research artifact.
---

# Craft Research Skill

**RPI Phase 1 of 3:** Research → Plan → Implement

Use this skill at the start of non-trivial features to explore the codebase and gather context before planning or implementing.

## Purpose

The Research phase prevents thrashing and discovers constraints early by:
- Exploring relevant parts of the codebase in parallel
- Identifying existing patterns and architectural boundaries
- Understanding dependencies and integration points
- Uncovering constraints before they become blockers

**Output:** Compact research artifact (~200 lines) at `docs/plans/YYYY-MM-DD-{topic}-research.md`

## When to Use

Use this skill when:
- Starting work on a non-trivial feature
- Need to understand how a new feature fits into existing architecture
- Want to discover existing patterns before creating new ones
- Need to identify integration points and dependencies
- Working in an unfamiliar part of the codebase

**Don't use** for:
- Simple bug fixes
- Trivial features with obvious implementation
- Research tasks (use Explore agent instead)

## Workflow

### 1. Define Research Scope

Ask the user to clarify the research scope if needed:
- What is the feature or capability being built?
- Which architectural boundaries are likely affected?
- Are there specific concerns or constraints?

### 2. Spawn Parallel Research Agents

Create multiple Explore agents to research different aspects concurrently:

```typescript
// Example: Researching "Add discount codes to orders"
Task: explore-existing-discount-logic
Task: explore-order-creation-flow
Task: explore-validation-patterns
Task: explore-database-schema-orders
```

**Research areas to explore:**
- Existing similar features or patterns
- Relevant core/ pure functions
- Related features/ use cases
- Database schema and repositories
- HTTP routes and validation schemas
- External integrations (if applicable)
- Test patterns for similar features

### 3. Synthesize Research Findings

After all agents complete, synthesize their findings into a compact artifact:

**Structure:**
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

### 4. Save Research Artifact

Write the synthesized research to:
```
docs/plans/YYYY-MM-DD-{topic}-research.md
```

Use kebab-case for the topic slug (e.g., `add-discount-codes-research.md`).

### 5. Prompt Human Review

End with a clear prompt:
```
Research complete. Please review the findings above.

Next steps:
- If research looks good, run `/craft-plan` to create implementation plan
- If more research needed, clarify what to explore and I'll continue
```

## Research Quality Standards

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

## Example Research Prompt

When user says:
> "I need to add discount codes to order creation"

Your research should explore:
1. **Existing discount logic** - Are there discounts already? Where?
2. **Order creation flow** - How are orders created today?
3. **Validation patterns** - How are other codes/slugs validated?
4. **Database schema** - Where would discount codes be stored?
5. **HTTP routes** - Which endpoints need modification?
6. **Test patterns** - How are similar features tested?

## Anti-Patterns to Avoid

❌ **Don't copy entire files** - Just note file paths and purpose
❌ **Don't write code yet** - This is research, not implementation
❌ **Don't create plans** - That's the next phase (`/craft-plan`)
❌ **Don't research sequentially** - Use parallel Explore agents
❌ **Don't include irrelevant details** - Stay focused on the feature

## After Research

Once research is complete and reviewed:
1. User reviews findings
2. User clarifies any open questions
3. Run `/craft-plan` with research artifact as input
4. Research artifact serves as context for planning

## Context Compaction

**Why research first?** The research phase is a compaction point:
- **Before research:** Entire codebase (too much context)
- **After research:** Compact artifact (~200 lines, only relevant details)
- **Planning phase** works from compact artifact, not raw codebase

This prevents context thrashing and keeps planning focused.
