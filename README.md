# Skill Factory

A Claude Code plugin that provides skills for software development best practices.

## Installation

Add this repository as a Claude Code plugin:

1. Clone the repository:
   ```bash
   git clone https://github.com/your-username/skill-factory
   ```

2. In Claude Code settings, add the path to this repository as a plugin

3. Restart Claude Code

All skills become available as `skill-factory:skill-name` and are automatically triggered when relevant.

## Available Skills

### Testing
- **tdd** - Test-driven development workflow
- **nullables** - Nullables testing pattern for isolation
- **approval-tests** - Approval testing techniques
- **bdd-with-approvals** - BDD combined with approval tests

### Design
- **hexagonal-architecture** - Ports and adapters architecture
- **event-modeling** - Event modeling design approach
- **collaborative-design** - Collaborative design sessions

### Practices
- **refactoring** - Safe refactoring techniques
- **refinement-loop** - Iterative refinement process
- **tech-writing-style** - Technical writing patterns

### Developer Tools
- **git-worktrees** - Git worktree workflows
- **using-uv** - Python uv package manager
- **writing-bash-scripts** - Bash script best practices

### AI
- **ai-patterns** - AI development patterns
- **creating-process-files** - Process file creation
- **creating-hooks** - Claude Code hooks

## How Skills Work

Skills solve the problem of limited context by releasing information gradually:
1. **Startup**: Only name + description loaded (~100 tokens per skill)
2. **When triggered**: Full instructions loaded
3. **As needed**: References loaded only when the task requires them

This keeps context lean while making rich knowledge available on demand.

## STARTER_CHARACTER

Each skill defines a `STARTER_CHARACTER = [emoji]` at the top. This is a visual indicator that Claude has loaded the skill and is following its instructions.

To activate this, add the following to your global `~/.claude/CLAUDE.md`:

```
Always start replies with STARTER_CHARACTER + space (default: 🍀). Stack emojis when requested, don't replace.
```

## Creating New Skills

Open this folder in Claude Code and ask it to create a new skill. It will guide you through the process.

## Updating Best Practices

```bash
./update-docs
```

Pulls latest skill patterns from Anthropic.
