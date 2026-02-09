# Skill Evaluation Framework - Research

**Date:** 2026-02-09
**Status:** Research Complete

## Summary

Researched official Anthropic patterns for Claude Code skill building and analyzed all 18 skills in the agentic-craft plugin. The goal: evaluate whether these skills form a solid foundation for craftsmanship with AI code generation, identify what to keep/cut/combine, and establish an evaluation framework.

## Evaluation Framework

### Five Criteria for Skill Quality

**1. Context Efficiency** (Does it earn its token cost?)
- SKILL.md under 500 lines (ideal: under 200)
- Progressive disclosure via references/ for details
- No redundant content Claude already knows
- Every line teaches Claude something it wouldn't do on its own

**2. Trigger Precision** (Does it fire correctly?)
- Description specific enough to avoid false positives
- Keywords cover natural phrasings (avoid false negatives)
- Clear boundary between this skill and neighbors
- Appropriate invocation mode (auto vs user-only)

**3. Behavioral Impact** (Does it change Claude's output?)
- Encodes opinionated methodology, not generic advice
- Anti-patterns that prevent specific failure modes
- Concrete rules Claude can follow, not philosophy
- Measurably different output with vs without the skill

**4. Composability** (Does it play well with others?)
- Clean boundaries with other skills (no overlap)
- Cross-references via `Skill()` invocation where useful
- Independent enough to use standalone
- Enhances rather than conflicts with other skills

**5. Craftsmanship Alignment** (Does it serve the mission?)
- Supports AI-assisted code generation quality
- Teaches patterns that survive refactoring
- Emphasizes boundaries, tests, and design
- Relevant to the day-to-day of working with AI agents

### Scoring: Keep / Edit / Combine / Remove

- **Keep**: Scores well on all 5 criteria, unique value
- **Edit**: Good concept but needs size reduction or clarity
- **Combine**: Overlapping skills that should merge
- **Remove**: Low behavioral impact, Claude already knows this, or niche

---

## Skill-by-Skill Evaluation

### Tier 1: Core Craftsmanship (Keep)

#### tdd (94 lines) - KEEP
- **Context efficiency**: Excellent. 94 lines, tight rules
- **Trigger precision**: "TDD", "test driven development" - clear
- **Behavioral impact**: HIGH. The 10 rules plus two-step red phase genuinely change Claude's TDD behavior. ZOMBIES checklist in reference is valuable
- **Composability**: Works standalone, referenced by RPI skills
- **Verdict**: Core skill. Concise, opinionated, effective

#### refactoring (92 lines) - KEEP
- **Context efficiency**: Good. 92 lines with focused workflow
- **Behavioral impact**: HIGH. The 4-stage workflow (prep, refactor, evaluate, summary) with "shift to critic mode" is genuinely useful
- **Composability**: Complements TDD (the REFACTOR in RED-GREEN-REFACTOR)
- **Verdict**: Essential companion to TDD

#### approval-tests (105 lines + references) - KEEP
- **Context efficiency**: Good progressive disclosure. 105-line overview, 20 reference files loaded by language
- **Behavioral impact**: HIGH. Claude doesn't naturally know ApprovalTests library APIs
- **Composability**: Referenced by bdd-with-approvals and event-modeling
- **Verdict**: Unique knowledge Claude lacks. Language-specific references are well-structured

#### hexagonal-architecture (98 lines) - KEEP
- **Context efficiency**: Excellent. 98 lines, crisp decision rule
- **Behavioral impact**: MEDIUM-HIGH. The "does it do I/O?" decision rule is simple but changes how Claude structures code
- **Composability**: Architectural foundation for other skills
- **Verdict**: Concise, opinionated, useful framing

### Tier 2: Valuable But Needs Editing

#### craft-tdd (532 lines) - COMBINE with tdd or EDIT heavily
- **Context efficiency**: POOR. 532 lines is the largest skill. Much content overlaps with `tdd`
- **Behavioral impact**: The L3/L4 boundary testing concept is genuinely valuable and unique. Property-based testing guidance is useful. But the TDD mechanics duplicate `tdd`
- **Recommendation**: Extract the unique content (L3/L4 boundaries, property-based testing, architecture-specific testing) into a reference file for `tdd`. Delete the duplicated TDD mechanics. Target: ~100 lines for SKILL.md with a `references/boundary-testing.md`

#### craft-plan (450 lines) - EDIT to reduce
- **Context efficiency**: POOR. 450 lines, much is template/example
- **Behavioral impact**: MEDIUM. The plan structure is useful but over-specified
- **Recommendation**: Cut to ~150 lines. The plan template should be in a reference file. The main SKILL.md needs only: when to use, inputs, outputs, structure overview, quality checklist

#### craft-implement (482 lines) - EDIT to reduce
- **Context efficiency**: POOR. 482 lines with verbose checklists
- **Behavioral impact**: MEDIUM. The phase-by-phase execution with test-first is valuable
- **Recommendation**: Cut to ~150 lines. Move detailed checklists and examples to reference files. Core value is: load plan, execute phase-by-phase, test-first at each phase, verify before proceeding

#### craft-research (240 lines) - EDIT to reduce
- **Context efficiency**: MODERATE. 240 lines, some template bloat
- **Behavioral impact**: MEDIUM. Parallel agent spawning is genuinely useful
- **Recommendation**: Cut to ~120 lines. The research template is too detailed in the main file. Move template to reference, keep workflow and anti-patterns

#### creating-hooks (244 lines) - EDIT to reduce
- **Context efficiency**: MODERATE. 244 lines. Has reference files with Anthropic docs
- **Behavioral impact**: MEDIUM. Useful niche skill for hook creation
- **Recommendation**: Cut SKILL.md to ~100 lines. The Anthropic reference docs do the heavy lifting. Main file just needs hook events, configuration pattern, and common examples

#### bdd-with-approvals (94 lines) - KEEP with minor edit
- **Context efficiency**: Good. 94 lines
- **Behavioral impact**: MEDIUM-HIGH. The "approved fixtures" concept is genuinely novel
- **Recommendation**: Keep as-is. The concept of designing scannable formats where correctness validates in <5 seconds is high-value

### Tier 3: Situational Value (Keep Small)

#### ai-patterns (144 lines) - KEEP but verify external dependency
- **Behavioral impact**: MEDIUM. Meta-patterns for working with AI are useful
- **Risk**: Depends on external git repo. If repo unavailable, skill degrades
- **Recommendation**: Keep. Consider inlining the most critical patterns to reduce external dependency

#### event-modeling (187 lines) - KEEP
- **Behavioral impact**: MEDIUM. Useful for domain-driven design, but niche
- **Composability**: Good - invokes bdd-with-approvals and approval-tests
- **Recommendation**: Keep at current size. Well-structured with references

#### collaborative-design (96 lines) - EVALUATE for overlap with craft-research
- **Behavioral impact**: LOW-MEDIUM. "Show don't tell" is good but vague
- **Overlap**: Conceptual overlap with craft-research (both are "before you code" skills)
- **Recommendation**: Consider combining the "design before code" philosophy into craft-research or making the distinction sharper. If kept, needs more concrete techniques

#### tech-writing-style (98 lines) - KEEP
- **Behavioral impact**: MEDIUM. The Fowler/Komoroske voice patterns are genuinely different from Claude's default
- **Niche**: Only relevant when writing blog posts/essays, not code
- **Recommendation**: Keep. Small, well-scoped, genuinely changes output

### Tier 4: Low Value (Remove or Minimize)

#### writing-bash-scripts (26 lines) - REMOVE
- **Behavioral impact**: LOW. Claude already knows `set -euo pipefail` and bash best practices
- **Content**: 26 lines of generic bash advice
- **Recommendation**: Remove. Move any unique rules (like "don't check for installed commands") into CLAUDE.md if they're project-specific preferences

#### git-worktrees (65 lines) - REMOVE
- **Behavioral impact**: LOW. Claude already knows git worktree commands
- **Content**: Mostly `git worktree` command reference
- **Recommendation**: Remove. The naming convention (`<project>_worktrees/`) can go in CLAUDE.md if it's a project preference

#### creating-process-files (76 lines) - REMOVE or make Claude-only
- **Behavioral impact**: LOW. The 4-phase process (interview, draft, clarify, simplify) is generic
- **Recommendation**: Remove. The concept of "text as code" is interesting but this skill doesn't encode enough specific knowledge to justify context cost. The skill creation process is better covered by the `docs/create_new_skill-process.md` file

#### refinement-loop (68 lines) - REMOVE
- **Behavioral impact**: LOW. Writing to numbered files in playground/ is a mechanical technique
- **Recommendation**: Remove. Claude can be told "iterate on this in playground/" in a single sentence. The core idea (fresh perspective from re-reading) doesn't need 68 lines

---

## Already-Removed Skills (Good Decisions)

#### nullables (161 lines + 2,439 lines of references) - CORRECTLY REMOVED
- Very niche testing pattern (Nullable infrastructure wrappers)
- 2,600 total lines of content - enormous context cost
- Useful concept but too specialized for a general plugin

#### using-uv (67 lines + 307 lines of references) - CORRECTLY REMOVED
- Generic tool documentation Claude already knows
- UV docs are easily fetchable from the web
- Low behavioral impact

---

## Recommended Actions Summary

| Skill | Lines | Action | Target Lines |
|-------|-------|--------|-------------|
| tdd | 94 | **Keep** | 94 |
| refactoring | 92 | **Keep** | 92 |
| approval-tests | 105 | **Keep** | 105 |
| hexagonal-architecture | 98 | **Keep** | 98 |
| bdd-with-approvals | 94 | **Keep** | 94 |
| tech-writing-style | 98 | **Keep** | 98 |
| ai-patterns | 144 | **Keep** | 144 |
| event-modeling | 187 | **Keep** | 187 |
| craft-tdd | 532 | **Combine** into tdd refs | 0 (content moves to tdd/references/) |
| craft-plan | 450 | **Edit** | ~150 |
| craft-implement | 482 | **Edit** | ~150 |
| craft-research | 240 | **Edit** | ~120 |
| creating-hooks | 244 | **Edit** | ~100 |
| collaborative-design | 96 | **Remove** | 0 |
| writing-bash-scripts | 26 | **Remove** | 0 |
| git-worktrees | 65 | **Remove** | 0 |
| creating-process-files | 76 | **Remove** | 0 |
| refinement-loop | 68 | **Remove** | 0 |

**Current total SKILL.md lines**: 3,191
**Projected total after changes**: ~1,432 (55% reduction)

---

## Structural Recommendations

### 1. Merge craft-tdd into tdd
The L3/L4 boundary testing and property-based testing concepts from craft-tdd are the real value. Move them to `tdd/references/boundary-testing.md`. Delete craft-tdd as a standalone skill. Update tdd's SKILL.md to reference boundary testing when working with layered architectures.

### 2. Shrink the RPI trilogy
craft-research, craft-plan, and craft-implement are individually too long. Each should be ~120-150 lines in SKILL.md with templates and detailed checklists in reference files. The methodology is sound; the encoding is bloated.

### 3. Remove "Claude already knows this" skills
writing-bash-scripts, git-worktrees, and refinement-loop encode knowledge Claude possesses natively. Creating-process-files duplicates the skill creation docs. Remove all four.

### 4. Remove collaborative-design
Drop collaborative-design entirely. craft-research covers the "before you build" space. Any useful "show don't tell" philosophy can be a one-liner in craft-research if needed.

### 5. Inline ai-patterns critical content
Remove the external git repo dependency from ai-patterns. Inline the most important patterns (context-rot, hallucination, focused-agent, chain-of-small-steps, knowledge-document) directly into the skill or its references. The external repo is a fragile dependency that degrades the skill when unavailable.

### 6. Strengthen the testing cluster
The testing cluster (tdd + approval-tests + bdd-with-approvals) is the strongest part of the plugin. With boundary-testing content from craft-tdd folded in, this becomes a comprehensive and well-composed testing toolkit.

---

## Resolved Decisions

1. **RPI trilogy stays as three separate skills** - craft-research, craft-plan, craft-implement remain independent. Each covers a distinct phase with different tool needs and triggers.
2. **Drop collaborative-design** - craft-research covers the pre-implementation space. No need for a second "before you build" skill.
3. **Inline ai-patterns content** - Remove external git repo dependency. Inline critical patterns directly into skill references.

## Remaining Open Question

- Are there missing skills the plugin should add (e.g., code review, debugging workflow)?

## Next Steps

Hand off to `/craft-plan` to create an implementation plan for the recommended changes.
