# Craft Implement: Workflow Details

This reference provides detailed templates, examples, and procedures for executing the implementation workflow.

## Phase Execution Pattern Template

Use this template for each phase in the implementation plan:

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

## L3/L4 Boundary Testing Examples

Ensure tests are at the right altitude with these patterns:

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

## Progress Reporting Templates

Use these templates to keep the user informed throughout implementation:

### Phase Start

```markdown
## Phase 2: Core Logic

**Goal:** Implement pure business logic for discount calculation
**Status:** 🔴 Starting
```

### Phase In Progress

```markdown
**Status:** 🔵 Writing tests (RED phase)
```

### Phase Complete

```markdown
**Status:** ✅ Complete

- Tests written and failed ✅
- Implementation passes tests ✅
- Phase verified ✅
```

### Overall Progress

```markdown
**Progress Update:**
- ✅ Phase 1: Database Schema - Complete
- ✅ Phase 2: Core Logic - Complete
- 🔵 Phase 3: Repository Layer - In Progress
- ⚪ Phase 4: Feature Use Case - Not Started
- ⚪ Phase 5: HTTP Routes - Not Started
- ⚪ Phase 6: Full Integration - Not Started

**Progress:** 2/6 phases complete (33%)
```

## Error Handling Procedures

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

## Example Execution Walkthrough

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

**Status:** 🔴 Starting

### Step 1: Write Failing Tests (RED)

**Test Spec from Plan:**
- Property test: discount reduces price correctly
- Example test: 10% discount on $100 order
- Edge case: expired discount code

**Action:** Creating test file

**File:** `src/core/discounts.test.ts`

```typescript
import { describe, it, expect } from 'bun:test';
import * as fc from 'fast-check';
import { applyDiscount, validateDiscountCode } from './discounts';

describe('Discount Logic', () => {
  it('should reduce price by discount percentage', () => {
    fc.assert(fc.property(
      fc.integer({ min: 1, max: 100000 }), // price in cents
      fc.integer({ min: 1, max: 100 }),    // discount percentage
      (price, percentage) => {
        const result = applyDiscount(price, percentage);
        expect(result).toBeLessThan(price);
        expect(result).toBe(price - Math.floor(price * percentage / 100));
      }
    ));
  });

  it('should apply 10% discount to $100 order', () => {
    const result = applyDiscount(10000, 10); // $100 in cents
    expect(result).toBe(9000); // $90 in cents
  });

  it('should reject expired discount codes', () => {
    const expiredDate = new Date('2020-01-01');
    const result = validateDiscountCode('EXPIRED10', expiredDate);
    expect(result.valid).toBe(false);
    expect(result.reason).toBe('expired');
  });
});
```

**Verification:**
```bash
bun run test
```

**Expected:** ❌ Tests MUST fail (functions don't exist yet)

**Result:** ✅ Tests failing - "Cannot find module './discounts'"

**Status:** 🔵 RED phase complete - proceeding to GREEN

---

### Step 2: Implement (GREEN)

**Action:** Creating implementation file

**File:** `src/core/discounts.ts`

```typescript
export function applyDiscount(priceInCents: number, percentage: number): number {
  return priceInCents - Math.floor(priceInCents * percentage / 100);
}

export function validateDiscountCode(
  code: string,
  expiresAt: Date
): { valid: boolean; reason?: string } {
  const now = new Date();
  if (expiresAt < now) {
    return { valid: false, reason: 'expired' };
  }
  return { valid: true };
}
```

**Verification:**
```bash
bun run test
```

**Expected:** ✅ Tests MUST pass

**Result:** ✅ All tests passing

**Status:** 🟢 GREEN phase complete - checking for refactoring

---

### Step 3: Refactor (If Needed)

**Action:** Code is clean and minimal - no refactoring needed

**Verification:**
```bash
bun run test
```

**Result:** ✅ Tests still passing

**Phase 2 Complete** ✅

**Progress:** 2/6 phases complete (33%)

---

## Phase 3: Repository Layer (L3 Boundary with Real Database)

**Goal:** Implement repository for discount code CRUD operations

**Status:** 🔴 Starting

...
```

## Quality Standards Checklists

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

## Final Verification Template

Use this template after all phases complete:

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
