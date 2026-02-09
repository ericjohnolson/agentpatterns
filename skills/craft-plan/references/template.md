# Implementation Plan Template

## Template Structure

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

---

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

---

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

---

## Quality Standards Checklists

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
