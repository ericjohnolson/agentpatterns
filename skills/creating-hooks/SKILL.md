---
name: creating-hooks
description: Creates Claude Code hooks for lifecycle events. Use when creating hooks, configuring PreToolUse/PostToolUse handlers, or automating Claude Code workflows.
license: MIT
compatibility: Claude Code plugin
metadata:
  author: eric-olson
  version: "1.0.0"
  workflow: development
  triggers:
    - "create hook"
    - "claude code hooks"
    - "lifecycle hooks"
    - "PreToolUse"
allowed-tools: Read Glob Write Bash
---

## Setup

First, update the reference docs to get the latest from Anthropic:
```bash
python ~/.claude/skills/creating-hooks/scripts/update-docs.py
```

## What Hooks Are

Shell commands that execute at lifecycle points in Claude Code. Unlike prompts, hooks are deterministic—they always run when triggered.

## Configuration

Settings files: `~/.claude/settings.json` (global), `.claude/settings.json` (project), `.claude/settings.local.json` (local).

```json
{
  "hooks": {
    "EventName": [{
      "matcher": "ToolPattern",
      "hooks": [{ "type": "command", "command": "your-command-here" }]
    }]
  }
}
```

**Matcher** (case-sensitive): Exact (`Write`), regex (`Edit|Write`), all (`*` or omit)

**Environment**: `$CLAUDE_PROJECT_DIR` (project root), `$CLAUDE_ENV_FILE` (SessionStart only)

## Hook Events

**Tool events** (matcher applies):
- `PreToolUse` - Before tool executes
- `PostToolUse` - After tool completes
- `PermissionRequest` - Permission dialog shown

**Session events**:
- `SessionStart` - Session begins/resumes (matcher: startup/resume/clear/compact)
- `SessionEnd` - Session ends
- `PreCompact` - Before compaction (matcher: manual/auto)

**Other events**:
- `UserPromptSubmit` - User submits prompt
- `Stop` - Agent finishes
- `SubagentStop` - Subagent finishes
- `Notification` - Alerts sent (matcher: notification type)

## Exit Codes

- **0**: Success. stdout shown in verbose mode. For `UserPromptSubmit`/`SessionStart`, stdout added to context.
- **2**: Block. stderr fed to Claude as error message. Blocks the action.
- **Other**: Non-blocking error. stderr shown to user.

## JSON Output

For advanced control, return JSON to stdout with exit code 0.

**PreToolUse** - Auto-approve or block tools:
```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",  // "allow", "deny", or "ask"
    "permissionDecisionReason": "Auto-approved",
    "updatedInput": { "field": "modified value" }
  }
}
```

**PostToolUse/Stop/SubagentStop** - Block actions with feedback:
```json
{
  "decision": "block",
  "reason": "Explanation fed to Claude"
}
```

**Generic stop control**:
```json
{
  "continue": false,
  "stopReason": "Message shown when stopping"
}
```

## Hook Input

Hooks receive JSON via stdin with fields: `session_id`, `transcript_path`, `cwd`, `permission_mode`, `hook_event_name`, `tool_name`, `tool_input`.

See [patterns.md](references/patterns.md) for common hook patterns including auto-formatting, command validation, context injection, notifications, and complete script examples.

## Anti-Patterns

- Using exit code 2 without stderr message (Claude gets no feedback)
- Forgetting to handle JSON parsing errors in scripts
- Blocking without explaining why (Claude will retry the same thing)
- Long-running hooks without timeout (default is 60s)
- Modifying files in PreToolUse (use PostToolUse for modifications)

## Reference

- [references/patterns.md](references/patterns.md) - Common hook patterns and script examples
- [references/anthropic-hooks.md](references/anthropic-hooks.md) - Complete reference (input schemas, prompt hooks, MCP tools)
- [references/anthropic-hooks-guide.md](references/anthropic-hooks-guide.md) - Quickstart and examples
