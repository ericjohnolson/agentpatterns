---
name: tdd
description: Boundary-focused TDD workflow enforcing L3/L4 altitude testing, property-based tests, and phase separation.
---

# TDD Skill

Use this skill when implementing features that require writing tests and code. This enforces boundary-focused test-driven development at L3/L4 altitude.

## Core Philosophy

**Tests must survive refactoring.** We test at architectural boundaries (L3/L4), not implementation details (L1/L2).

- **L1/L2 unit tests go extinct** under AI refactoring because they couple to implementation
- **L3/L4 boundary tests survive** because they describe stable contracts and observable behavior
- **Humans own specs, AI owns implementation** - tests are the contract

See [AGENTS.md](../../../AGENTS.md) for complete testing philosophy.

## TDD Workflow

**Write tests first with strict phase separation.** Follow this cycle for every feature:

### Red → Green → Refactor Cycle

1. **RED** - Write FAILING test at L3/L4 boundary
2. **STOP** - Run test, confirm it fails for the right reason
3. **DO NOT PROCEED** until test fails - never write implementation yet
4. **GREEN** - Write minimal code to make test pass
5. **VERIFY** - Run test, confirm it passes
6. **REFACTOR** - Clean up while keeping tests green

**Critical:** Never combine red and green phases. The test MUST fail before you write implementation.

## Test Altitude Mapped to FCIS Layers

| Altitude | Layer | What to Test | How | Test Location |
|----------|-------|-------------|-----|---------------|
| **L3** | `core/` public API | Behavioral invariants (properties, not examples) | Vitest + fast-check | `tests/unit/core/{module}/` |
| **L3** | `features/` public API | Use case outcomes with real database | Vitest + Testcontainers | `tests/integration/features/` |
| **L4** | `shell/http/` routes | HTTP contract: status, response shapes, errors | Supertest | `tests/e2e/` |
| **L4** | `shell/external/` | Contract with Square API | Vitest + mocked HTTP | `tests/integration/external/` |

**What NOT to test (L1/L2):**
- Private functions or internal helpers
- Repository implementation details
- How data transforms internally
- Internal function call sequences

## Test Writing Order (Per Feature)

When implementing a vertical slice feature, write tests in this sequence:

| Step | Layer | Test Altitude | Run Command | Verify Fails |
|------|-------|---------------|-------------|--------------|
| 1 | Core | **L3 Properties** | `bun run test` | ✅ MUST see failure |
| 2 | Features | **L3 Behavior** | `bun run test:integration` | ✅ MUST see failure |
| 3 | HTTP Routes | **L4 Contract** | `bun run test:e2e` | ✅ MUST see failure |

**After writing each test, STOP and verify failure before implementing.**

## The Five Testing Rules

### 1. Survive Refactoring

Tests MUST survive complete refactoring of internals. If a test breaks when you rename a variable or move a function without changing behavior, the test is at the wrong altitude.

**Good:** `expect(response.status).toBe(201)`
**Bad:** `expect(mockRepository.create).toHaveBeenCalled()`

### 2. Phase Separation

Write a FAILING test first. Do NOT write implementation yet. Never combine red and green.

**Workflow:**
1. Write test
2. Run test
3. STOP - confirm failure
4. Write implementation
5. Run test
6. Confirm pass

### 3. Mock Constraint

Only mock at the **system boundary** (external HTTP APIs). Never mock internal modules.

**Allowed:**
- Mocking Square API responses
- Mocking Stripe webhooks

**Forbidden:**
- Mocking internal repositories
- Mocking `core/` functions
- Mocking `features/` use cases
- Using in-memory DB instead of Testcontainers

### 4. No Tautological Tests

Assert on **observable behavior**, not implementation calls.

**Good:** `expect(order.total).toBe(150)`
**Bad:** `expect(mockPricing.calculate).toHaveBeenCalledWith(cart)`

### 5. Property-Based for core/

Pure functions should have property-based tests. Test invariants, not examples.

**Properties:** round-trip, idempotency, boundary preservation, commutativity, associativity

## Property-Based Testing with fast-check

For `core/` pure functions, use fast-check to test invariants:

### Basic Property Test

```typescript
// tests/unit/core/pricing/split-amount.test.ts
import { describe, it } from 'vitest';
import fc from 'fast-check';
import { splitAmount } from '@/core/pricing/split-amount';

describe('splitAmount', () => {
  it('should preserve total when splitting across parts', () => {
    fc.assert(
      fc.property(
        fc.integer({ min: 0, max: 1000000 }),
        fc.integer({ min: 1, max: 10 }),
        (total, parts) => {
          const split = splitAmount(total, parts);
          return split.reduce((sum, n) => sum + n, 0) === total;
        }
      )
    );
  });

  it('should create exact number of parts requested', () => {
    fc.assert(
      fc.property(
        fc.integer({ min: 0, max: 1000000 }),
        fc.integer({ min: 1, max: 10 }),
        (total, parts) => {
          const split = splitAmount(total, parts);
          return split.length === parts;
        }
      )
    );
  });

  it('should produce non-negative values', () => {
    fc.assert(
      fc.property(
        fc.integer({ min: 0, max: 1000000 }),
        fc.integer({ min: 1, max: 10 }),
        (total, parts) => {
          const split = splitAmount(total, parts);
          return split.every(n => n >= 0);
        }
      )
    );
  });
});
```

### Common Property Patterns

**Round-trip:**
```typescript
it('should round-trip through encode/decode', () => {
  fc.assert(
    fc.property(fc.anything(), (value) => {
      return decode(encode(value)) === value;
    })
  );
});
```

**Idempotency:**
```typescript
it('should be idempotent', () => {
  fc.assert(
    fc.property(fc.string(), (input) => {
      return normalize(normalize(input)) === normalize(input);
    })
  );
});
```

**Boundary preservation:**
```typescript
it('should preserve validity through transformation', () => {
  fc.assert(
    fc.property(validInputArbitrary, (validInput) => {
      const result = transform(validInput);
      return isValid(result);
    })
  );
});
```

## Behavioral Assertion Tests (Features)

Test use case outcomes with real database. Assert behavior at L3 boundary:

```typescript
// tests/integration/features/orders/create-order.test.ts
import { describe, it, expect, beforeAll, afterAll } from 'vitest';
import { PostgreSqlContainer, StartedPostgreSqlContainer } from '@testcontainers/postgresql';
import { createOrder } from '@/features/orders/create-order';
import { seedTickets } from '@/test-utils/seed';

describe('createOrder', () => {
  let container: StartedPostgreSqlContainer;

  beforeAll(async () => {
    container = await new PostgreSqlContainer().start();
    // Setup DB connection
  });

  afterAll(async () => {
    await container.stop();
  });

  describe('when all tickets are available', () => {
    it('creates order with correct total from multiple tickets', async () => {
      // Arrange
      await seedTickets([
        { id: 't1', price: 50, available: true },
        { id: 't2', price: 30, available: true }
      ]);

      // Act
      const result = await createOrder({
        ticketIds: ['t1', 't2'],
        customerId: 'c1'
      });

      // Assert - test BEHAVIOR at L3 boundary
      expect(result.success).toBe(true);
      if (result.success) {
        expect(result.data.total).toBe(80);
        expect(result.data.tickets).toHaveLength(2);
        expect(result.data.status).toBe('pending');
      }

      // ❌ DON'T assert implementation:
      // expect(mockRepository.create).toHaveBeenCalledWith(...)
      // expect(mockPricing.calculate).toHaveBeenCalled()
    });
  });

  describe('when ticket is not found', () => {
    it('returns error with descriptive message', async () => {
      // Act
      const result = await createOrder({
        ticketIds: ['nonexistent'],
        customerId: 'c1'
      });

      // Assert - test ERROR behavior at L3 boundary
      expect(result.success).toBe(false);
      if (!result.success) {
        expect(result.error).toContain('Ticket not found');
      }
    });
  });

  describe('when ticket is not available', () => {
    it('returns error indicating unavailability', async () => {
      // Arrange
      await seedTickets([
        { id: 't1', price: 50, available: false }
      ]);

      // Act
      const result = await createOrder({
        ticketIds: ['t1'],
        customerId: 'c1'
      });

      // Assert - test ERROR behavior at L3 boundary
      expect(result.success).toBe(false);
      if (!result.success) {
        expect(result.error).toContain('not available');
      }
    });
  });
});
```

**Why real database?** Tests realistic behavior. No mocking internal repositories. Catches integration bugs.

## HTTP Contract Tests (Routes)

Test the HTTP contract at L4 boundary: status codes, response shapes, error formats:

```typescript
// tests/e2e/orders.test.ts
import { describe, it, expect } from 'vitest';
import request from 'supertest';
import { app } from '@/shell/http/app';
import { seedTickets } from '@/test-utils/seed';

describe('POST /orders', () => {
  describe('when request is valid', () => {
    it('returns 201 with order data', async () => {
      // Arrange
      await seedTickets([{ id: 't1', price: 50, available: true }]);

      // Act
      const response = await request(app)
        .post('/orders')
        .send({ ticketIds: ['t1'], customerId: 'c1' });

      // Assert - test HTTP CONTRACT at L4 boundary
      expect(response.status).toBe(201);
      expect(response.body).toMatchObject({
        id: expect.any(String),
        total: 50,
        status: 'pending',
        tickets: expect.arrayContaining([
          expect.objectContaining({ id: 't1' })
        ])
      });
    });
  });

  describe('when ticket not found', () => {
    it('returns 400 with error message', async () => {
      // Act
      const response = await request(app)
        .post('/orders')
        .send({ ticketIds: ['nonexistent'], customerId: 'c1' });

      // Assert - test ERROR CONTRACT at L4 boundary
      expect(response.status).toBe(400);
      expect(response.body).toMatchObject({
        error: expect.stringContaining('Ticket not found')
      });
    });
  });

  describe('when request body is invalid', () => {
    it('returns 400 with validation error', async () => {
      // Act
      const response = await request(app)
        .post('/orders')
        .send({ ticketIds: 'not-an-array' }); // Invalid

      // Assert - test VALIDATION CONTRACT at L4 boundary
      expect(response.status).toBe(400);
      expect(response.body.error).toBeDefined();
    });
  });
});
```

**Why test HTTP contract?** It's the stable boundary between frontend and backend. Implementation can change, contract stays the same.

## Test Hygiene Standards

### Naming Convention
Describe the behavior, not the implementation:
```typescript
// ✅ GOOD - describes behavior
it('rejects order when ticket unavailable', ...)
it('calculates total from all ticket prices', ...)

// ❌ BAD - describes implementation
it('calls checkAvailability function', ...)
it('uses reduce to sum prices', ...)
```

### AAA Pattern (Arrange-Act-Assert)
```typescript
it('applies percentage discount to base price', () => {
  // Arrange - set up test data
  const basePrice = 100;
  const discount = { type: 'percentage', value: 20 };

  // Act - call the function under test
  const result = applyDiscount(basePrice, discount);

  // Assert - verify the outcome
  expect(result).toBe(80);
});
```

### Isolation Rules
- Each test must be independent - no shared mutable state
- Use `beforeEach` for setup that varies per test
- Clean database tables in `beforeEach` (integration/e2e)
- Never rely on test execution order

### One Behavior Per Test
- Test one logical behavior per `it()` block
- Multiple `expect()` is OK if verifying one outcome
- If test name needs "and", split into two tests

### Minimal Fixtures
- Create test data inline when simple
- Use factory functions for complex objects
- Only include fields relevant to the test

## Feature Implementation Checklist

When implementing a vertical slice feature, follow this checklist:

### Phase 1: Tests (RED)

**For core/ pure functions:**
- [ ] Write property-based tests with fast-check (test invariants, not examples)
- [ ] Run `bun run test` - verify tests fail
- [ ] STOP - do not implement yet

**For features/ use cases:**
- [ ] Write behavioral assertion tests with real database (Testcontainers)
- [ ] Run `bun run test:integration` - verify tests fail
- [ ] STOP - do not implement yet

**For shell/http/ routes:**
- [ ] Write HTTP contract tests (status codes, response shapes)
- [ ] Run `bun run test:e2e` - verify tests fail
- [ ] STOP - do not implement yet

### Phase 2: Implementation (GREEN)

- [ ] Implement pure functions in `core/` - make property tests pass
- [ ] Run `bun run test` - verify green
- [ ] Implement feature orchestration in `features/` - make behavior tests pass
- [ ] Run `bun run test:integration` - verify green
- [ ] Implement HTTP routes in `shell/http/` - make contract tests pass
- [ ] Run `bun run test:e2e` - verify green

### Phase 3: Refactor & Verify

- [ ] Refactor code while keeping tests green
- [ ] Run full suite: `bun run test && bun run test:integration && bun run test:e2e`
- [ ] All tests pass
- [ ] No skipped tests
- [ ] Tests still describe behavior, not implementation

## Testing Commands

```bash
bun run test                        # L3 core property tests
bun run test path/to/file.test.ts  # Single test file
bun run test:watch                  # Watch mode
bun run test:integration            # L3 feature behavior tests
bun run test:e2e                    # L4 HTTP contract tests
bun run test:coverage               # With coverage
```

## Anti-Patterns (FORBIDDEN)

### Never Assert Mock Calls for Internal Functions

```typescript
// ❌ FORBIDDEN - testing implementation
expect(mockRepository.create).toHaveBeenCalledWith({ ... });
expect(mockPricing.calculate).toHaveBeenCalled();

// ✅ CORRECT - testing behavior
expect(order.total).toBe(expectedTotal);
expect(result.success).toBe(true);
```

### Never Delete Failing Tests

If a test fails:
1. Fix the implementation (if test is correct)
2. Fix the test (if it's at wrong altitude)
3. Discuss with human (if requirement changed)

Never delete a test just to make the suite pass.

### Never Combine Red and Green

```typescript
// ❌ FORBIDDEN
// 1. Write test
// 2. Write implementation immediately
// 3. Run test (see it pass)

// ✅ CORRECT
// 1. Write test
// 2. Run test (MUST see failure)
// 3. STOP
// 4. Write implementation
// 5. Run test (see it pass)
```

### Never Mock Internal Modules

```typescript
// ❌ FORBIDDEN
vi.mock('@/shell/database/repositories/order-repository');
vi.mock('@/core/pricing/calculate-discount');
vi.mock('@/features/orders/create-order');

// ✅ CORRECT - only mock external systems
vi.mock('@/shell/external/square-client');
```

### Never Snapshot Test (Except Small Data)

Snapshot tests are forbidden except for small serialized data structures (< 20 lines).

**Forbidden:**
- Snapshot testing entire API responses
- Snapshot testing rendered output

**Allowed:**
- Snapshot testing a small config object

## If User Asks to Skip Tests

If explicitly requested, you may implement first, but should:
1. **Note that this violates TDD** and tests are deferred
2. **Write tests immediately after** implementation
3. **Verify tests would have caught bugs** (intentionally break implementation, see tests fail)
4. **Remind user** that test-first prevents wasted implementation effort
