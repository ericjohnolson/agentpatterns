# agentic-craft

A Claude Code plugin providing skills for software development best practices.

## Overview

This plugin offers curated skills that guide Claude through proven software development workflows including test-driven development, architectural patterns, refactoring techniques, and AI development practices.

## Installation

### From GitHub

In Claude Code, add the marketplace and install the plugin:

```
/plugin marketplace add ericjohnolson/agentic-craft
/plugin install agentic-craft@agentic-craft
```

### From Local Directory

1. Clone the repository:
   ```bash
   git clone https://github.com/ericjohnolson/agentic-craft
   ```

2. In Claude Code, add the marketplace and install:
   ```
   /plugin marketplace add /path/to/agentic-craft
   /plugin install agentic-craft@agentic-craft
   ```

### Updating

To get the latest version:

```
/plugin marketplace update agentic-craft
```

After installation, skills become available as `agentic-craft:skill-name` and are automatically triggered when relevant to your task.

## Available Skills

### Testing
| Skill | Description |
|-------|-------------|
| `tdd` | Boundary-focused TDD workflow enforcing L3/L4 altitude testing, property-based tests, and phase separation. Includes references for ZOMBIES checklist and boundary testing patterns. |
| `approval-tests` | Writes approval tests (snapshot/golden master testing) for Python, JavaScript/TypeScript, or Java. Use for verifying complex output, characterization testing legacy code, or testing combinations. |
| `bdd-with-approvals` | Scannable BDD tests written in domain language. Creates approved fixtures and executable specifications where correctness validates in seconds. |

### Architecture & Design
| Skill | Description |
|-------|-------------|
| `hexagonal-architecture` | Applies hexagonal (ports & adapters) architecture with a single decision rule: "Does it do I/O?" determines inside vs outside. |
| `event-modeling` | Designs systems using Event Modeling with STATE_CHANGE, STATE_VIEW, and AUTOMATION slices. Conversational design through domain events and vertical slices. |

### Development Practices
| Skill | Description |
|-------|-------------|
| `refactoring` | Structured refactoring with test safety. Four stages: prep, main refactoring, final evaluation, summary. Enforces small changes with test verification. |
| `tech-writing-style` | Technical writing for blog posts and essays in conversational expert style. References Fowler and Komoroske patterns for personal, concrete-first voice. |

### RPI Methodology (Research, Plan, Implement)
| Skill | Description |
|-------|-------------|
| `craft-research` | Phase 1: Spawns parallel subagents to explore codebase and produces a compact research artifact (~200 lines). Prevents thrashing by discovering constraints early. |
| `craft-plan` | Phase 2: Consumes research artifact and produces compact implementation plan with L3/L4 test specs and phased execution steps. |
| `craft-implement` | Phase 3: Loads plan and executes phase by phase with strict RED-GREEN-REFACTOR discipline. Verifies each phase before proceeding. |

### AI & Tooling
| Skill | Description |
|-------|-------------|
| `ai-patterns` | Reference catalog of patterns for AI-augmented development. Covers context management, reliability, and communication patterns by Lexler, Falco, Ordog, and Avni. |
| `creating-hooks` | Creates Claude Code hooks for lifecycle events (PreToolUse, PostToolUse, SessionStart, etc.). Covers configuration, exit codes, JSON control, and common patterns. |

## Usage Examples

**Start TDD workflow:**
```
/tdd
```

**Research before building a feature:**
```
/craft-research add user authentication
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
