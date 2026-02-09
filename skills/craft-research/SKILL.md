---
name: craft-research
description: Research phase of RPI methodology. Spawns parallel subagents to explore codebase and produces compact research artifact. Use at the start of non-trivial features to explore before planning.
license: MIT
compatibility: Claude Code plugin
metadata:
  author: eric-olson
  version: "1.0.0"
  workflow: research
  triggers:
    - "craft research"
    - "research codebase for feature"
    - "RPI research"
allowed-tools: Read Glob Grep Task
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

After all agents complete, synthesize their findings into a compact artifact (~200 lines).

Write research artifact using the [template](references/template.md). See template for full structure, quality standards, and example research scenarios.

### 4. Save Research Artifact

Write to `docs/plans/YYYY-MM-DD-{topic}-research.md` using kebab-case for topic.

### 5. Prompt Human Review

Prompt user to review findings and either proceed to `/craft-plan` or request additional research.

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
