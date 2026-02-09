# Skill Consolidation - Implementation Plan

**Date:** 2026-02-09
**Status:** Plan - Ready for Review
**Research:** `docs/plans/2026-02-09-skill-evaluation-framework-research.md`

## Goal

Reduce plugin context cost by 55% (3,191 → ~1,432 SKILL.md lines) by removing low-value skills, merging craft-tdd into tdd, shrinking the RPI trilogy and creating-hooks, and inlining ai-patterns content to remove the external repo dependency.

## Acceptance Criteria

- [ ] 5 skills removed: collaborative-design, writing-bash-scripts, git-worktrees, creating-process-files, refinement-loop
- [ ] craft-tdd merged into tdd (unique L3/L4 content becomes `tdd/references/boundary-testing.md`)
- [ ] craft-research SKILL.md ≤ 120 lines (template moves to reference)
- [ ] craft-plan SKILL.md ≤ 150 lines (template moves to reference)
- [ ] craft-implement SKILL.md ≤ 150 lines (verbose examples/checklists move to reference)
- [ ] creating-hooks SKILL.md ≤ 120 lines (common patterns move to reference)
- [ ] ai-patterns self-contained: external repo dependency removed, critical patterns inlined into references
- [ ] marketplace.json keywords updated (remove nullables, git-worktrees; add craft)
- [ ] All remaining skills load and display correctly via `/` menu

## Files to Delete

### Skill Directories (complete removal)
- `skills/collaborative-design/` (SKILL.md + references/)
- `skills/writing-bash-scripts/` (SKILL.md only)
- `skills/git-worktrees/` (SKILL.md only)
- `skills/creating-process-files/` (SKILL.md + references/)
- `skills/refinement-loop/` (SKILL.md only)
- `skills/craft-tdd/` (SKILL.md only - after content migrated to tdd/)

## Files to Create

- `skills/tdd/references/boundary-testing.md` - L3/L4 boundary testing from craft-tdd
- `skills/craft-research/references/template.md` - Research artifact template
- `skills/craft-plan/references/template.md` - Plan artifact template
- `skills/craft-implement/references/workflow-detail.md` - Verbose checklists/examples
- `skills/creating-hooks/references/patterns.md` - Common hook patterns
- `skills/ai-patterns/references/patterns-catalog.md` - Inlined pattern descriptions

## Files to Modify

- `skills/tdd/SKILL.md` - Add reference to boundary-testing.md, minor description update
- `skills/craft-research/SKILL.md` - Shrink to ~120 lines, move template to reference
- `skills/craft-plan/SKILL.md` - Shrink to ~150 lines, move template to reference
- `skills/craft-implement/SKILL.md` - Shrink to ~150 lines, move examples to reference
- `skills/creating-hooks/SKILL.md` - Shrink to ~120 lines, move patterns to reference
- `skills/ai-patterns/SKILL.md` - Remove external repo dependency, reference inlined catalog
- `.claude-plugin/marketplace.json` - Update keywords

## Implementation Phases

### Phase 1: Delete Low-Value Skills

**Goal:** Remove 5 skills that encode knowledge Claude already has

**Tasks:**
1. Delete `skills/collaborative-design/` directory
2. Delete `skills/writing-bash-scripts/` directory
3. Delete `skills/git-worktrees/` directory
4. Delete `skills/creating-process-files/` directory
5. Delete `skills/refinement-loop/` directory

**Verification:**
- [ ] 5 directories deleted
- [ ] 13 remaining skill directories in `skills/`

---

### Phase 2: Merge craft-tdd into tdd

**Goal:** Preserve L3/L4 boundary testing content while eliminating TDD duplication

**Tasks:**
1. Create `skills/tdd/references/boundary-testing.md` with craft-tdd's unique content:
   - L3/L4 altitude testing concept and table (lines 46-59 of craft-tdd)
   - Test writing order per feature (lines 61-71)
   - The 5 testing rules: survive refactoring, phase separation, mock constraint, no tautological tests, property-based for core (lines 73-119)
   - Property-based testing patterns with fast-check (lines 121-209)
   - Behavioral assertion test pattern (lines 211-301)
   - HTTP contract test pattern (lines 303-368)
   - Anti-patterns section (lines 464-517)
   - Omit: TDD workflow (duplicates tdd), checklists (redundant), test hygiene (generic), commands (project-specific)
   - Target: ~200 lines
2. Update `skills/tdd/SKILL.md`:
   - Add to description: mention boundary testing and property-based tests
   - Add section after Core Rules: "For layered architectures, see [boundary-testing.md](references/boundary-testing.md) for L3/L4 altitude testing, property-based tests, and architecture-specific patterns"
   - Keep existing content unchanged
3. Delete `skills/craft-tdd/` directory

**Verification:**
- [ ] `tdd/references/boundary-testing.md` exists with ~200 lines
- [ ] `tdd/SKILL.md` references boundary-testing.md
- [ ] `craft-tdd/` directory deleted
- [ ] No content lost - all unique craft-tdd concepts preserved in reference

---

### Phase 3: Shrink craft-research

**Goal:** Cut from 240 to ~120 lines by moving template to reference

**Tasks:**
1. Create `skills/craft-research/references/template.md` containing the research artifact template (current lines 82-161)
2. Rewrite `skills/craft-research/SKILL.md`:
   - Keep: frontmatter, purpose, when to use, workflow steps (condensed), anti-patterns, context compaction
   - Move: full template to reference file
   - Replace template with: "Write research artifact using [template](references/template.md)"
   - Remove: example research prompt (lines 204-216), quality standards checklists (lines 183-203) - move to template file
   - Target: ~120 lines

**Verification:**
- [ ] SKILL.md ≤ 120 lines
- [ ] Template accessible via reference
- [ ] Workflow still clear and complete

---

### Phase 4: Shrink craft-plan

**Goal:** Cut from 450 to ~150 lines by moving template to reference

**Tasks:**
1. Create `skills/craft-plan/references/template.md` containing:
   - Full plan template structure (current lines 73-311)
   - Test specification guidelines (current lines 356-405)
   - Phase boundary guidelines (current lines 407-425)
2. Rewrite `skills/craft-plan/SKILL.md`:
   - Keep: frontmatter, purpose, when to use, workflow steps (condensed), anti-patterns, context compaction
   - Move: full template, test spec guidelines, phase boundary guidelines to reference
   - Replace template with: "Write plan artifact using [template](references/template.md)"
   - Remove: quality standards checklists (move to template), "after planning" section (obvious)
   - Target: ~150 lines

**Verification:**
- [ ] SKILL.md ≤ 150 lines
- [ ] Template accessible via reference
- [ ] Workflow still clear and complete

---

### Phase 5: Shrink craft-implement

**Goal:** Cut from 482 to ~150 lines by moving verbose examples to reference

**Tasks:**
1. Create `skills/craft-implement/references/workflow-detail.md` containing:
   - Phase execution pattern template (current lines 70-145)
   - L3/L4 boundary testing examples (current lines 256-304)
   - Progress reporting templates (current lines 306-335)
   - Error handling procedures (current lines 337-361)
   - Example execution walkthrough (current lines 413-472)
2. Rewrite `skills/craft-implement/SKILL.md`:
   - Keep: frontmatter, purpose, when to use, workflow (condensed), test-first discipline rules, anti-patterns
   - Move: all code examples, templates, progress reporting format, error handling detail, example execution to reference
   - Core SKILL.md should be: load plan → execute phases (RED→GREEN→REFACTOR) → verify → report → final check
   - Target: ~150 lines

**Verification:**
- [ ] SKILL.md ≤ 150 lines
- [ ] Detailed workflow accessible via reference
- [ ] Core test-first discipline preserved in SKILL.md

---

### Phase 6: Shrink creating-hooks

**Goal:** Cut from 244 to ~120 lines by moving common patterns to reference

**Tasks:**
1. Create `skills/creating-hooks/references/patterns.md` containing:
   - Common patterns section (current lines 147-205): auto-format, block dangerous commands, inject context, desktop notification
   - Hook scripts section (current lines 207-231)
2. Rewrite `skills/creating-hooks/SKILL.md`:
   - Keep: frontmatter, what hooks are, configuration, hook events, exit codes, JSON output, anti-patterns, reference links
   - Move: common patterns and hook scripts to new reference file
   - Add reference link: "See [patterns.md](references/patterns.md) for common hook patterns and script examples"
   - Target: ~120 lines

**Verification:**
- [ ] SKILL.md ≤ 120 lines
- [ ] Patterns accessible via reference
- [ ] Core hook concepts still in SKILL.md

---

### Phase 7: Inline ai-patterns

**Goal:** Remove external git repo dependency, make skill self-contained

**Tasks:**
1. Create `skills/ai-patterns/references/patterns-catalog.md` with expanded descriptions for each pattern:
   - For each pattern/anti-pattern/obstacle currently listed in SKILL.md, write a 3-5 line description explaining what it is, when it applies, and how to use it
   - Organize by category: Context Management, Reliability & Quality, Communication, Additional
   - Include attribution to Lexler, Falco, Ördög, Avni
   - Target: ~250 lines (enough detail to be self-contained)
2. Update `skills/ai-patterns/SKILL.md`:
   - Remove "First Step" section (lines 22-27) that clones external repo
   - Remove "Patterns Location" section (lines 29-30)
   - Remove "Browse All" section (lines 133-140) with ls commands
   - Remove "Online" section (line 142-144)
   - Keep the pattern index/catalog (lines 34-129) - this is the overview
   - Add: "For detailed descriptions of each pattern, see [patterns-catalog.md](references/patterns-catalog.md)"
   - Target: ~110 lines
3. Delete `skills/ai-patterns/scripts/ensure-patterns-repo` script
4. Remove `skills/ai-patterns/scripts/` directory

**Verification:**
- [ ] SKILL.md ~110 lines, no external repo references
- [ ] patterns-catalog.md contains expanded descriptions
- [ ] scripts/ directory removed
- [ ] Skill is fully self-contained

---

### Phase 8: Update marketplace.json and final cleanup

**Goal:** Update metadata to reflect current skill set

**Tasks:**
1. Update `.claude-plugin/marketplace.json`:
   - Remove "nullables" from keywords (already deleted)
   - Remove "git-worktrees" from keywords (now deleted)
   - Verify remaining keywords match actual skills
2. Update `docs/map.md` if it exists - reflect new structure

**Verification:**
- [ ] marketplace.json keywords match actual skills
- [ ] All 13 remaining skill directories present:
  tdd, refactoring, approval-tests, hexagonal-architecture, bdd-with-approvals,
  tech-writing-style, ai-patterns, event-modeling, craft-research, craft-plan,
  craft-implement, creating-hooks, creating-process-files... wait - creating-process-files is removed
  Actually 12: tdd, refactoring, approval-tests, hexagonal-architecture,
  bdd-with-approvals, tech-writing-style, ai-patterns, event-modeling,
  craft-research, craft-plan, craft-implement, creating-hooks
- [ ] No broken references between skills

## Constraints

- **No behavioral changes to kept skills** - Only restructuring (moving content to references), not rewriting logic
- **Preserve all unique content** - When merging craft-tdd, every non-redundant concept must survive in tdd/references/
- **Progressive disclosure** - SKILL.md stays concise, references hold detail
- **Attribution** - ai-patterns must maintain credit to Lexler, Falco, Ördög, Avni

## Out of Scope

- Rewriting skill descriptions through the 7-lens process (could improve trigger precision but separate effort)
- Adding new skills (code review, debugging - flagged in research as potential additions)
- Changing frontmatter structure or plugin architecture
- Testing with different models (Haiku, Sonnet, Opus) to verify behavioral impact

## Next Steps

After review and approval:
1. Run `/craft-implement` to execute phases 1-8
2. Each phase is independent except Phase 2 (create reference before deleting craft-tdd)
3. Phases 3-7 can be done in parallel if desired
