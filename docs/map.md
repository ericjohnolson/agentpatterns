# Skill Factory Structure

```
skill-factory/
├── CLAUDE.md                           # Agent instructions for this project
├── update-docs                         # Bash wrapper to update documentation
├── scripts/                            # Automation scripts
│   ├── sources.txt                     # URLs to fetch docs from
│   └── fetch_anthropic_skill_docs.py   # Fetch latest Anthropic docs
├── docs/                               # All knowledge about creating skills
│   ├── knowledge/
│   │   └── anthropic-skill-docs/       # Official Anthropic skill documentation
│   │       ├── overview.md             # What skills are, why they exist, core concepts
│   │       ├── skills.md               # Implementation syntax, structure, usage patterns
│   │       └── best-practices.md       # Proven patterns, common pitfalls, guidelines
│   ├── create_new_skill-process.md     # Instructions for creating skills
│   ├── map.md                          # This file - repository structure
│   └── project.md                      # Project-specific information
└── skills/                             # Plugin skills (flat structure)
    ├── tdd/                            # Test-driven development
    ├── nullables/                      # Nullables testing pattern
    ├── approval-tests/                 # Approval testing
    ├── bdd-with-approvals/             # BDD with approval tests
    ├── hexagonal-architecture/         # Ports and adapters architecture
    ├── event-modeling/                 # Event modeling design
    ├── collaborative-design/           # Collaborative design sessions
    ├── refactoring/                    # Safe refactoring practices
    ├── refinement-loop/                # Iterative refinement process
    ├── tech-writing-style/             # Technical writing patterns
    ├── git-worktrees/                  # Git worktree workflows
    ├── using-uv/                       # Python uv package manager
    ├── writing-bash-scripts/           # Bash script best practices
    ├── ai-patterns/                    # AI development patterns
    ├── creating-process-files/         # Process file creation
    └── creating-hooks/                 # Claude Code hooks
```

## Purpose

- **docs/**: Contains all instructional material the agent uses to create skills
- **skills/**: Plugin skills directory - each subdirectory contains a SKILL.md and optional references
- **CLAUDE.md**: Provides context to the agent about this repository's purpose

## Plugin Usage

This repository is a Claude Code plugin. Skills are namespaced as `skill-factory:skill-name` and discovered automatically when the plugin is enabled.
