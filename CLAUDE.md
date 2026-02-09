# CLAUDE.md

This file provides guidance to Claude Code when working with code in this repository.

## Purpose

This is a Claude Code plugin (`agentic-craft`) providing skills for software development best practices including TDD, refactoring, architecture patterns, and AI development workflows.

## Plugin Structure

```
.claude-plugin/
  marketplace.json     # Marketplace and plugin manifest
skills/
  <skill-name>/
    SKILL.md           # Main skill content (frontmatter + instructions)
    references/        # Optional reference files loaded on demand
    assets/            # Optional templates and output resources
    scripts/           # Optional deterministic helper scripts
```

## Common Commands

```bash
./update-docs    # Fetch latest Anthropic skill documentation
```

## Creating Skills

When the user asks to create a new skill, follow instructions in `docs/create_new_skill-process.md`.

## Documentation

- `docs/project.md` - Project goals and workflow
- `docs/map.md` - Complete repository structure
