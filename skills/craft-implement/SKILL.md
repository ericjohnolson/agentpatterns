---
name: craft-implement
description: Implement phase of RPI methodology. Loads plan and executes phase by phase with test-first approach. Use when executing an implementation plan from craft-plan.
license: MIT
compatibility: Claude Code plugin
metadata:
  author: eric-olson
  version: "1.0.0"
  workflow: development
  triggers:
    - "craft implement"
    - "execute implementation plan"
    - "RPI implement"
allowed-tools: Read Glob Write Bash
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

For each phase in the plan, execute the RED → GREEN → REFACTOR cycle:

**RED Phase:**
1. Write test(s) according to spec from plan
2. Ensure tests assert behavior at L3/L4 boundary
3. Run test command
4. Verify tests FAIL for the right reason
5. STOP - do not proceed until verified

**GREEN Phase:**
1. Write minimal code to make tests pass
2. Follow architectural patterns from plan
3. Run test command
4. Verify tests PASS

**REFACTOR Phase:**
1. Clean up code if needed (keep tests green)
2. Extract common patterns
3. Improve naming
4. Run test command
5. Verify tests still PASS

For detailed templates and examples, see [workflow-detail.md](references/workflow-detail.md).

### 3. Verify After Each Phase

After completing each phase, verify:
- [ ] Tests were written first (RED)
- [ ] Tests failed for the right reason
- [ ] Implementation makes tests pass (GREEN)
- [ ] Tests assert at L3/L4 boundaries
- [ ] Phase-specific verification steps completed

**If verification fails, STOP and fix before proceeding.**

### 4. Report Progress Incrementally

After each phase, report progress:
```markdown
**Progress Update:**
- ✅ Phase 1: Database Schema - Complete
- ✅ Phase 2: Core Logic - Complete
- 🔵 Phase 3: Repository Layer - In Progress
- ⚪ Phase 4: Feature Use Case - Not Started
```

See [workflow-detail.md](references/workflow-detail.md) for progress reporting templates.

### 5. Final Verification

After all phases complete:
- Run full test suite (unit, integration, e2e)
- Verify all acceptance criteria met
- Manual smoke test if applicable
- Document next steps (commit, PR, follow-up)

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

## Summary

The Implement phase:
- Executes plan mechanically, phase by phase
- Enforces strict test-first discipline (RED → GREEN → REFACTOR)
- Tests at L3/L4 boundaries (behavior, not implementation)
- Verifies each phase before proceeding
- Reports progress incrementally
- Produces working feature with passing tests
