---
name: craft-plan
description: Plan phase of RPI methodology. Consumes research artifact or topic and produces compact implementation plan with L3/L4 test specs. Use after craft-research or to start planning a non-trivial feature.
license: MIT
compatibility: Claude Code plugin
metadata:
  author: eric-olson
  version: "1.0.0"
  workflow: planning
  triggers:
    - "craft plan"
    - "create implementation plan"
    - "RPI plan"
allowed-tools: Read Glob Write AskUserQuestion
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

Write plan artifact using the [template structure](references/template.md).

**Required sections:**
- **Goal** - 1-2 sentence description of what we're building and why
- **Acceptance Criteria** - Testable outcomes as checklist
- **Files to Create** - Organized by layer (Core, Features, Shell, Tests)
- **Files to Modify** - What changes in existing files
- **Implementation Phases** - Ordered steps with:
  - Clear goal per phase
  - Test specifications at L3/L4 boundaries
  - Tasks list (RED → GREEN → REFACTOR for tested phases)
  - Verification checklist
- **Constraints & Considerations** - Architectural, testing, performance, security notes
- **Out of Scope** - Explicitly deferred features
- **Approval Checklist** - Pre-implementation verification
- **Next Steps** - Path to `/craft-implement`

**Phase ordering:**
1. Database Schema (if needed)
2. Core Logic (L3 boundary - property tests)
3. Repository Layer (if needed)
4. Feature Use Cases (L3 boundary - behavioral assertions)
5. HTTP Routes (L4 boundary - contract tests)
6. Full Integration (verification)

See [template.md](references/template.md) for:
- Complete plan structure with all sections
- Test specification examples and guidelines
- Phase boundary guidelines
- Quality standards checklists

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

## Test Specifications at Boundaries

### L3 Core Tests (Property-Based)
Use fast-check to test invariant properties, not example cases.

**Good:** Properties that must always hold
**Bad:** Example-based assertions

### L3 Feature Tests (Behavioral Assertions)
Test behavior at use case boundaries with real database (Testcontainers).

**Good:** `expect(result.success).toBe(true)`
**Bad:** `expect(mockRepository.create).toHaveBeenCalledWith(...)`

### L4 HTTP Tests (Contract)
Test HTTP contract: status codes, response shapes, error formats.

**Good:** `expect(response.status).toBe(201)`
**Bad:** Testing internal implementation details

See [template.md](references/template.md) for detailed examples.

## Phase Boundaries

Each phase should:
1. **Have a clear goal** - What capability is being added?
2. **Be independently verifiable** - Can you confirm it works?
3. **Be in logical order** - Database → Core → Features → Routes
4. **Be small enough** - 30 minutes to 2 hours of work

Good boundaries: Database schema, Core functions, Repository, Feature use cases, HTTP routes

Bad boundaries: "Implement everything", mixing multiple layers

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
