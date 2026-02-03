# agentic-craft

A Claude Code plugin providing skills for software development best practices.

## Overview

This plugin offers curated skills that guide Claude through proven software development workflows including test-driven development, architectural patterns, refactoring techniques, and AI development practices.

## Installation

Add this repository as a Claude Code plugin:

```bash
git clone https://github.com/your-username/agentic-craft
```

In Claude Code settings, add the path to this repository as a plugin, then restart Claude Code.

Skills become available as `agentic-craft:skill-name` and are automatically triggered when relevant to your task.

## Available Skills

### Testing
| Skill | Description |
|-------|-------------|
| `tdd` | Test-driven development workflow with red-green-refactor cycle |
| `nullables` | Nullables testing pattern for dependency isolation |
| `approval-tests` | Approval testing techniques for complex output verification |
| `bdd-with-approvals` | Behavior-driven development combined with approval tests |

### Architecture & Design
| Skill | Description |
|-------|-------------|
| `hexagonal-architecture` | Ports and adapters architecture pattern |
| `event-modeling` | Event modeling design approach for system design |
| `collaborative-design` | Facilitated collaborative design sessions |

### Development Practices
| Skill | Description |
|-------|-------------|
| `refactoring` | Safe refactoring techniques with test coverage |
| `refinement-loop` | Iterative refinement process for quality improvement |
| `tech-writing-style` | Technical writing patterns for clear documentation |

### Developer Tools
| Skill | Description |
|-------|-------------|
| `git-worktrees` | Git worktree workflows for parallel development |
| `using-uv` | Python uv package manager best practices |
| `writing-bash-scripts` | Bash script development patterns |

### AI Development
| Skill | Description |
|-------|-------------|
| `ai-patterns` | AI development patterns and practices |
| `creating-process-files` | Process file creation for workflows |
| `creating-hooks` | Claude Code hooks development |

## Usage Examples

**Start TDD workflow:**
```
/tdd
```

**Apply hexagonal architecture:**
```
/hexagonal-architecture
```

**Refactor code safely:**
```
/refactoring
```

## How Skills Work

Skills release information gradually to preserve context:
1. **Startup**: Only name + description loaded (~100 tokens per skill)
2. **Triggered**: Full instructions loaded when relevant
3. **On demand**: References loaded only when needed

## Creating New Skills

Open this folder in Claude Code and ask it to create a new skill. It will guide you through the process.

## Updating Documentation

```bash
./update-docs
```

Fetches latest skill patterns from Anthropic.

## Author

Eric Olson

## License

See LICENSE file.
