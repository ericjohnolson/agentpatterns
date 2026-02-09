---
name: craft-implement
description: Implement phase of RPI methodology. Loads plan and executes phase by phase with test-first approach.
---

# Craft Implement Skill

**RPI Phase 3 of 3:** Research → Plan → **Implement**

Use this skill to execute an implementation plan phase by phase with strict test-first discipline.

## Purpose

The Implement phase executes a compact plan mechanically by:
- Loading the implementation plan
- Executing each phase in order
- Writing failing tests first (L3/L4 boundaries)
- Implementing to pass tests
- Verifying before proceeding
- Reporting progress incrementally

**Input:** Implementation plan at `docs/plans/YYYY-MM-DD-{topic}-plan.md`
**Output:** Working feature with passing tests

## When to Use

Use this skill when:
- Have a complete implementation plan from `/craft-plan`
- Ready to execute plan phase by phase
- Want strict test-first discipline enforced
- Need incremental progress reporting

**Don't use** for:
- Exploratory coding without a plan
- Simple bug fixes
- Research or planning tasks

## Workflow

### 1. Load Implementation Plan

Read the plan artifact:
```bash
Read: docs/plans/YYYY-MM-DD-{topic}-plan.md
```

Extract:
- Implementation phases
- Test specifications for each phase
- Acceptance criteria
- Verification steps

### 2. Execute Phases Sequentially

For each phase in the plan:

#### Phase Execution Pattern

```markdown
## Phase {N}: {Phase Name}

**Goal:** {Phase goal from plan}

**Status:** 🔴 Starting

### Step 1: Write Failing Tests (RED)

**Test Spec from Plan:**
{Copy test spec from plan}

**Action:**
- Create test file
- Write test(s) according to spec
- Ensure tests assert behavior at L3/L4 boundary

**Verification:**
```bash
bun run test  # or test:integration, or test:e2e
```

**Expected:** ❌ Tests MUST fail

**Status:** {✅ Tests failing / ❌ Tests not failing - STOP}

---

### Step 2: Implement (GREEN)

**Action:**
- Write minimal code to make tests pass
- Follow architectural patterns from plan
- Use existing patterns from research

**Verification:**
```bash
bun run test  # or test:integration, or test:e2e
```

**Expected:** ✅ Tests MUST pass

**Status:** {✅ Tests passing / ❌ Tests not passing - debug}

---

### Step 3: Refactor (If Needed)

**Action:**
- Clean up code while keeping tests green
- Extract common patterns
- Improve naming

**Verification:**
```bash
bun run test  # or test:integration, or test:e2e
```

**Expected:** ✅ Tests still pass

**Status:** {✅ Tests still passing}

---

### Phase {N} Complete ✅

**Verification checklist:**
- [ ] Tests written first and failed
- [ ] Implementation makes tests pass
- [ ] Tests assert behavior, not implementation
- [ ] Phase verification steps from plan completed

**Proceeding to Phase {N+1}**

---
```

### 3. Verify After Each Phase

After completing each phase, verify:
- [ ] Tests were written first (RED)
- [ ] Tests failed for the right reason
- [ ] Implementation makes tests pass (GREEN)
- [ ] Tests assert at L3/L4 boundaries
- [ ] Phase-specific verification steps completed

**If verification fails, STOP and fix before proceeding.**

### 4. Report Progress Incrementally

After each phase, report:
```markdown
**Progress Update:**
- ✅ Phase 1: Database Schema - Complete
- ✅ Phase 2: Core Logic - Complete
- 🔵 Phase 3: Repository Layer - In Progress
- ⚪ Phase 4: Feature Use Case - Not Started
- ⚪ Phase 5: HTTP Routes - Not Started
- ⚪ Phase 6: Full Integration - Not Started
```

### 5. Final Verification

After all phases complete:

```markdown
## Final Verification

### Full Test Suite
```bash
bun run test && bun run test:integration && bun run test:e2e
```

**Result:** {✅ All passing / ❌ Failures found}

### Acceptance Criteria

From plan:
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

### Manual Smoke Test

{If applicable, test key user journeys manually}

**Result:** {✅ Working / ❌ Issues found}

---

## Implementation Complete

All phases executed successfully:
- ✅ All tests passing
- ✅ All acceptance criteria met
- ✅ No test skips
- ✅ Code follows architectural patterns

**Next steps:**
- Run `/commit` to create commit
- Create PR if needed
- Document any follow-up work
```

## Test-First Discipline

**CRITICAL:** For every phase, tests MUST be written first and MUST fail before implementation.

### Enforcement Rules

1. **Write test first**
   - Create test file
   - Write test(s) according to spec
   - Do NOT write implementation yet

2. **Verify RED**
   - Run test command
   - Confirm test fails
   - Confirm it fails for the right reason
   - STOP - do not proceed until verified

3. **Implement GREEN**
   - Write minimal code to pass
   - Run test command
   - Confirm test passes

4. **Refactor**
   - Clean up if needed
   - Run test command
   - Confirm tests still pass

### If Tests Don't Fail

If tests pass immediately (before implementation):
1. **STOP** - test is tautological or wrong
2. **Fix test** - ensure it tests behavior, not implementation
3. **Re-verify RED** - test must fail before proceeding

### If Tests Don't Pass

If tests don't pass after implementation:
1. **Debug** - find the issue
2. **Fix implementation** - make tests pass
3. **Don't skip tests** - never comment out or delete failing tests
4. **Don't change tests** - unless test spec is wrong

## L3/L4 Boundary Testing

Ensure tests are at the right altitude:

### L3 Core Tests (Property-Based)

```typescript
// ✅ CORRECT - tests invariant property
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

// ❌ WRONG - tests example, not property
it('should split 100 into 4 parts of 25', () => {
  expect(split(100, 4)).toEqual([25, 25, 25, 25]);
});
```

### L3 Feature Tests (Behavioral Assertions)

```typescript
// ✅ CORRECT - tests behavior at boundary
expect(result.success).toBe(true);
expect(result.data.total).toBe(80);

// ❌ WRONG - tests implementation
expect(mockRepository.create).toHaveBeenCalledWith({ total: 80 });
```

### L4 HTTP Tests (Contract)

```typescript
// ✅ CORRECT - tests HTTP contract
expect(response.status).toBe(201);
expect(response.body).toMatchObject({
  id: expect.any(String),
  total: expect.any(Number)
});

// ❌ WRONG - tests implementation
expect(createOrderHandler).toHaveBeenCalled();
```

## Progress Reporting

Report progress after each phase to keep user informed:

### Phase Start
```
## Phase 2: Core Logic

**Goal:** Implement pure business logic for discount calculation
**Status:** 🔴 Starting
```

### Phase In Progress
```
**Status:** 🔵 Writing tests (RED phase)
```

### Phase Complete
```
**Status:** ✅ Complete

- Tests written and failed ✅
- Implementation passes tests ✅
- Phase verified ✅
```

### Overall Progress
```
**Progress:** 2/6 phases complete (33%)
```

## Error Handling

### If Phase Fails

If a phase cannot be completed:
1. **Document the issue** - What failed and why
2. **Ask for guidance** - Should we adjust the plan?
3. **Don't skip ahead** - Phases depend on previous phases
4. **Don't work around** - Fix the root cause

### If Tests Won't Pass

If tests won't pass after reasonable effort:
1. **Review test spec** - Is the spec achievable?
2. **Review implementation** - Is there a bug?
3. **Ask for help** - Explain what's blocking
4. **Don't delete tests** - Tests define the contract

### If Plan is Incomplete

If plan is missing details:
1. **Note the gap** - What's unclear?
2. **Ask for clarification** - Get specifics before proceeding
3. **Don't guess** - Assumptions cause rework
4. **Don't improvise** - Stick to the plan or update it

## Quality Standards

### Test Quality
- [ ] Tests written before implementation
- [ ] Tests assert at L3/L4 boundaries
- [ ] Tests verify behavior, not implementation calls
- [ ] Property-based tests for core/ functions
- [ ] Real database for features/ (Testcontainers)
- [ ] HTTP contract for routes/

### Implementation Quality
- [ ] Follows FCIS architecture (pure core, imperative shell)
- [ ] Uses existing patterns from research
- [ ] Minimal code to pass tests
- [ ] Clear naming and structure
- [ ] No TODOs or commented code

### Phase Verification
- [ ] Each phase verified before proceeding
- [ ] Full test suite passes
- [ ] All acceptance criteria met
- [ ] No skipped tests

## Anti-Patterns to Avoid

❌ **Don't implement before tests** - Violates test-first discipline
❌ **Don't skip RED verification** - Must confirm failure before implementing
❌ **Don't delete failing tests** - Fix implementation or test spec instead
❌ **Don't mock internal modules** - Only mock external boundaries
❌ **Don't jump phases** - Execute in order, each depends on previous
❌ **Don't improvise** - Stick to the plan or update it explicitly

## After Implementation

Once all phases complete and verified:
1. **Run full test suite** - Confirm everything passes
2. **Verify acceptance criteria** - All must be met
3. **Manual smoke test** - Try key user journeys
4. **Create commit** - Use `/commit` skill
5. **Document follow-up** - Note any deferred work

## Context Compaction

**Why implement from plan?** The implement phase works from compact spec:
- **Before implementation:** Raw codebase + research + planning (too much context)
- **During implementation:** Compact plan (~200 lines, clear phases)
- **Each phase** is a checkpoint - verify before proceeding

This prevents context thrashing and keeps implementation focused on one phase at a time.

## Example Execution

```markdown
# Implementing: Add Discount Codes to Orders

**Plan:** docs/plans/2026-02-07-add-discount-codes-plan.md
**Status:** Phase 1 of 6 in progress

---

## Phase 1: Database Schema

**Goal:** Add discount_codes table and order_discounts junction table

**Status:** 🔴 Starting

### Step 1: Modify Schema

Created:
- `src/db/schema/discount-codes.ts` - discount_codes table
- `src/db/schema/order-discounts.ts` - order_discounts junction table

### Step 2: Generate Migration

```bash
bun run db:generate
```

**Result:** ✅ Migration generated: 0001_add_discount_codes.sql

### Step 3: Apply Migration

```bash
bun run db:migrate
```

**Result:** ✅ Migration applied successfully

### Step 4: Verify

```bash
bun run db:studio
```

**Result:** ✅ Tables visible in database studio

**Phase 1 Complete** ✅

**Progress:** 1/6 phases complete (17%)

---

## Phase 2: Core Logic (L3 Boundary)

**Goal:** Implement pure discount validation and calculation logic

**Status:** 🔵 Writing tests (RED phase)

...
```

## Summary

The Implement phase:
- Executes plan mechanically, phase by phase
- Enforces strict test-first discipline (RED → GREEN → REFACTOR)
- Tests at L3/L4 boundaries (behavior, not implementation)
- Verifies each phase before proceeding
- Reports progress incrementally
- Produces working feature with passing tests
