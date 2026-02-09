---
name: craft-plan
description: Plan phase of RPI methodology. Consumes research artifact or topic and produces compact implementation plan with L3/L4 test specs.
---

# Craft Plan Skill

**RPI Phase 2 of 3:** Research → **Plan** → Implement

Use this skill after research (or standalone) to create a compact implementation plan with test specifications at L3/L4 boundaries.

## Purpose

The Plan phase creates a compact spec that fits in context by:
- Defining files to create/modify in order of operations
- Specifying test specs as behavioral assertions at L3/L4 boundaries
- Setting clear acceptance criteria
- Identifying phase boundaries for incremental implementation

**Output:** Compact plan (~200 lines) at `docs/plans/YYYY-MM-DD-{topic}-plan.md`

## When to Use

Use this skill when:
- Completing research phase (has research artifact)
- Starting a non-trivial feature (no research artifact, will create one)
- Need a clear implementation roadmap before coding
- Want to specify test boundaries before implementing

**Don't use** for:
- Simple bug fixes
- Trivial features with obvious implementation
- Exploratory spikes

## Workflow

### 1. Consume Research Artifact (If Exists)

If research artifact exists:
```bash
# Read the research artifact
Read: docs/plans/YYYY-MM-DD-{topic}-research.md
```

If no research artifact, create one inline (condensed version):
- Identify relevant files
- Note existing patterns
- List integration points

### 2. Define Plan Scope

Ask the user to clarify if needed:
- What are the acceptance criteria?
- Are there specific constraints or preferences?
- What's the priority (MVP vs full feature)?

### 3. Create Implementation Plan

Structure the plan with these sections:

**Structure:**
```markdown
# {Feature Name} - Implementation Plan

**Date:** YYYY-MM-DD
**Status:** Plan - Ready for Review

## Goal

1-2 sentence description of what we're building and why.

## Acceptance Criteria

- [ ] Criterion 1 (testable outcome)
- [ ] Criterion 2 (testable outcome)
- [ ] Criterion 3 (testable outcome)

## Files to Create

### Core Layer
- `src/core/{module}/{function-name}.ts` - {purpose}

### Features Layer
- `src/features/{domain}/{use-case}.ts` - {purpose}

### Shell Layer
- `src/shell/database/repositories/{entity}-repository.ts` - {purpose}
- `src/shell/http/routes/{entity}.ts` - {purpose}
- `src/shell/http/schemas/{entity}.ts` - {purpose}

### Tests
- `tests/unit/core/{module}/{function}.test.ts` - L3 property tests
- `tests/integration/features/{feature}.test.ts` - L3 behavior tests
- `tests/e2e/{entity}.test.ts` - L4 HTTP contract tests

## Files to Modify

- `src/db/schema/{entity}.ts` - {what changes}
- `src/shell/http/app.ts` - {register new routes}

## Implementation Phases

### Phase 1: Database Schema
**Goal:** Add database support for {entity}

**Tasks:**
1. Add {table} to schema
2. Generate migration
3. Apply migration

**Verification:**
- [ ] Migration applied successfully
- [ ] `bun run db:studio` shows new table

---

### Phase 2: Core Logic (L3 Boundary)
**Goal:** Implement pure business logic for {capability}

**Test Spec (Property-Based):**
```typescript
// tests/unit/core/{module}/{function}.test.ts
describe('{functionName}', () => {
  it('should {invariant property}', () => {
    fc.assert(
      fc.property(arbitrary, (input) => {
        const result = functionName(input);
        return {invariant holds};
      })
    );
  });
});
```

**Tasks:**
1. Write property tests (RED)
2. Run tests, verify failure
3. Implement pure function (GREEN)
4. Refactor if needed

**Verification:**
- [ ] `bun run test` passes
- [ ] Property tests validate invariants

---

### Phase 3: Repository Layer
**Goal:** Add database operations for {entity}

**Tasks:**
1. Create repository with CRUD operations
2. Integration tested via feature layer (next phase)

**Verification:**
- [ ] Repository created
- [ ] Ready for feature layer

---

### Phase 4: Feature Use Case (L3 Boundary)
**Goal:** Orchestrate {use case} with real database

**Test Spec (Behavioral Assertions):**
```typescript
// tests/integration/features/{feature}.test.ts
describe('{useCaseName}', () => {
  it('should {expected behavior} when {condition}', async () => {
    // Arrange
    await seed({test data});

    // Act
    const result = await useCaseName({input});

    // Assert - test BEHAVIOR at boundary
    expect(result.success).toBe(true);
    expect(result.data.{field}).toBe({expected});
  });

  it('should return error when {error condition}', async () => {
    // Act
    const result = await useCaseName({invalid input});

    // Assert - test ERROR behavior
    expect(result.success).toBe(false);
    expect(result.error).toContain('{expected error}');
  });
});
```

**Tasks:**
1. Write behavioral assertion tests with Testcontainers (RED)
2. Run tests, verify failure
3. Implement feature orchestration (GREEN)
4. Refactor if needed

**Verification:**
- [ ] `bun run test:integration` passes
- [ ] Tests verify behavior, not implementation calls

---

### Phase 5: HTTP Routes (L4 Boundary)
**Goal:** Expose {use case} via HTTP API

**Test Spec (HTTP Contract):**
```typescript
// tests/e2e/{entity}.test.ts
describe('POST /{resource}', () => {
  it('returns 201 with {entity} data on success', async () => {
    // Act
    const response = await request(app)
      .post('/{resource}')
      .send({valid request});

    // Assert - test HTTP CONTRACT
    expect(response.status).toBe(201);
    expect(response.body).toMatchObject({
      {expected shape}
    });
  });

  it('returns 400 with error when {validation fails}', async () => {
    // Act
    const response = await request(app)
      .post('/{resource}')
      .send({invalid request});

    // Assert - test ERROR CONTRACT
    expect(response.status).toBe(400);
    expect(response.body.error).toBeDefined();
  });
});
```

**Tasks:**
1. Create Zod validation schema
2. Write HTTP contract tests (RED)
3. Run tests, verify failure
4. Implement route handler (GREEN)
5. Register route in app
6. Refactor if needed

**Verification:**
- [ ] `bun run test:e2e` passes
- [ ] Tests verify HTTP contract (status, shape, errors)

---

### Phase 6: Full Integration
**Goal:** Verify entire feature works end-to-end

**Tasks:**
1. Run full test suite
2. Manual smoke test via API
3. Verify acceptance criteria

**Verification:**
- [ ] All tests pass (`bun run test && bun run test:integration && bun run test:e2e`)
- [ ] All acceptance criteria met
- [ ] No test skips
- [ ] No console errors

## Constraints & Considerations

### Architectural
- {Constraint 1}
- {Constraint 2}

### Testing
- Use property-based tests for core/ (fast-check)
- Use real database for features/ (Testcontainers)
- Test HTTP contract for routes/ (status codes, shapes)

### Performance
- {Consideration if applicable}

### Security
- {Consideration if applicable}

## Out of Scope

- {Feature explicitly not included}
- {Enhancement deferred to later}

## Approval Checklist

Before implementing, verify:
- [ ] All files to create/modify listed
- [ ] Implementation phases have clear boundaries
- [ ] Test specs defined at L3/L4 boundaries
- [ ] Acceptance criteria are testable
- [ ] Constraints documented
- [ ] Out of scope items noted

## Next Steps

After human review and approval:
1. Run `/craft-implement` to execute this plan phase by phase
2. Each phase: write failing tests → implement → verify → proceed
```

### 4. Save Plan Artifact

Write the plan to:
```
docs/plans/YYYY-MM-DD-{topic}-plan.md
```

Use kebab-case for the topic slug (e.g., `add-discount-codes-plan.md`).

### 5. Prompt Human Review

End with a clear prompt:
```
Implementation plan complete. Please review the plan above.

Next steps:
- If plan looks good, run `/craft-implement` to execute it
- If changes needed, clarify what to adjust and I'll update the plan
- If significant changes, consider re-running `/craft-research` first
```

## Plan Quality Standards

### Completeness
- [ ] All files to create/modify listed with purpose
- [ ] Implementation phases have clear boundaries
- [ ] Test specs defined for each L3/L4 boundary
- [ ] Acceptance criteria are specific and testable
- [ ] Constraints and considerations documented
- [ ] Out of scope items explicitly noted

### Conciseness
- [ ] Plan is ~200 lines or less
- [ ] Test specs are templates, not full implementations
- [ ] Each phase has clear goal and verification
- [ ] No unnecessary detail

### Actionability
- [ ] Phase order is logical (database → core → features → routes)
- [ ] Each phase can be implemented independently
- [ ] Verification steps are concrete
- [ ] Ready to hand off to `/craft-implement`

## Test Specification Guidelines

### L3 Core Tests (Property-Based)

Always specify properties to test, not examples:
```typescript
// ✅ GOOD - property specification
it('should preserve total when splitting', () => {
  fc.assert(fc.property(
    fc.integer({ min: 0, max: 1000000 }),
    fc.integer({ min: 1, max: 10 }),
    (total, parts) => {
      const result = split(total, parts);
      return result.reduce((sum, n) => sum + n) === total;
    }
  ));
});

// ❌ BAD - example-based
it('should split 100 into 4 parts of 25', () => {
  expect(split(100, 4)).toEqual([25, 25, 25, 25]);
});
```

### L3 Feature Tests (Behavioral Assertions)

Specify behavior at the use case boundary:
```typescript
// ✅ GOOD - behavior specification
expect(result.success).toBe(true);
expect(result.data.total).toBe(expectedTotal);

// ❌ BAD - implementation specification
expect(mockRepository.create).toHaveBeenCalledWith(...);
```

### L4 HTTP Tests (Contract)

Specify HTTP contract:
```typescript
// ✅ GOOD - contract specification
expect(response.status).toBe(201);
expect(response.body).toMatchObject({
  id: expect.any(String),
  total: expect.any(Number)
});

// ❌ BAD - internal behavior
expect(createOrderHandler).toHaveBeenCalled();
```

## Phase Boundary Guidelines

Each phase should:
1. **Have a clear goal** - What capability is being added?
2. **Be independently verifiable** - Can you confirm it works?
3. **Be in logical order** - Database → Core → Features → Routes
4. **Be small enough** - 30 minutes to 2 hours of work

**Good phase boundaries:**
- Database schema
- Core pure functions
- Repository operations
- Feature use cases
- HTTP routes

**Bad phase boundaries:**
- "Implement everything"
- "Add tests and code"
- Mixing multiple layers in one phase

## Anti-Patterns to Avoid

❌ **Don't write full implementations** - Specs only, not code
❌ **Don't skip test specifications** - Tests define the contract
❌ **Don't mix L1/L2 tests** - Only L3/L4 boundaries
❌ **Don't create giant phases** - Keep phases small and focused
❌ **Don't omit verification steps** - Each phase needs concrete checks

## After Planning

Once plan is complete and reviewed:
1. User reviews plan
2. User approves or requests changes
3. Run `/craft-implement` with plan artifact as input
4. Implementation executes phase by phase with test-first approach

## Context Compaction

**Why plan after research?** The plan phase is a compaction point:
- **Before planning:** Research artifact (~200 lines of findings)
- **After planning:** Implementation spec (~200 lines of actionable phases)
- **Implementation phase** works from compact plan, not raw research

This prevents context thrashing and keeps implementation focused.
