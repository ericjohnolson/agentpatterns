# Common Hook Patterns and Examples

This document provides reusable patterns for Claude Code hooks with practical examples.

## Pattern: Auto-format After Edit

Automatically format files after writing or editing them.

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{
        "type": "command",
        "command": "jq -r '.tool_input.file_path' | xargs -I{} sh -c 'echo {} | grep -q \"\\.ts$\" && npx prettier --write {}'"
      }]
    }]
  }
}
```

**How it works**: After any Edit or Write tool completes, extracts the file path from the tool input JSON, checks if it's a TypeScript file, and runs prettier on it.

## Pattern: Block Dangerous Commands

Validate Bash commands before execution to prevent destructive operations.

```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "hooks": [{
        "type": "command",
        "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/validate-bash.py"
      }]
    }]
  }
}
```

**Example validation script** (`.claude/hooks/validate-bash.py`):

```python
#!/usr/bin/env -S uv run --script
# /// script
# dependencies = []
# requires-python = ">=3.11"
# ///

import json
import sys
import re

data = json.load(sys.stdin)
command = data.get("tool_input", {}).get("command", "")

# Block force-push to main/master
if re.search(r'git\s+push.*--force.*\b(main|master)\b', command):
    print("Blocked: Force push to main/master is not allowed", file=sys.stderr)
    sys.exit(2)

# Block rm -rf on critical directories
if re.search(r'rm\s+-rf\s+(/|~|\$HOME)', command):
    print("Blocked: Dangerous rm -rf on critical directory", file=sys.stderr)
    sys.exit(2)

sys.exit(0)
```

## Pattern: Inject Context on Prompt

Add reminders or context to every user prompt automatically.

```json
{
  "hooks": {
    "UserPromptSubmit": [{
      "hooks": [{
        "type": "command",
        "command": "echo '[REMINDER: Follow TDD practices]'"
      }]
    }]
  }
}
```

**How it works**: On every prompt submission, the stdout (exit code 0) is automatically added to the conversation context. Claude sees this reminder with each request.

## Pattern: Desktop Notification

Send OS-level notifications when Claude needs input.

**macOS**:
```json
{
  "hooks": {
    "Notification": [{
      "hooks": [{
        "type": "command",
        "command": "osascript -e 'display notification \"Claude needs input\" with title \"Claude Code\"'"
      }]
    }]
  }
}
```

**Linux**:
```json
{
  "hooks": {
    "Notification": [{
      "hooks": [{
        "type": "command",
        "command": "notify-send 'Claude Code' 'Claude needs input'"
      }]
    }]
  }
}
```

## Hook Script Template

For complex logic, use external scripts. UV single-file format provides clean dependency management:

```python
#!/usr/bin/env -S uv run --script
# /// script
# dependencies = []
# requires-python = ">=3.11"
# ///

import json
import sys

# Read hook input from stdin
data = json.load(sys.stdin)
tool_input = data.get("tool_input", {})
hook_event = data.get("hook_event_name")

# Your validation/processing logic here
should_block = False
error_message = ""

if should_block:
    print(error_message, file=sys.stderr)
    sys.exit(2)  # Exit code 2 blocks the action

# Optional: Return JSON for advanced control
output = {
    "continue": True
}
print(json.dumps(output))
sys.exit(0)
```

**Make executable**: `chmod +x your-script.py`

**UV requirements**: Install with `curl -LsSf https://astral.sh/uv/install.sh | sh`

## Advanced: PreToolUse Auto-Approval

Automatically approve specific tools to avoid permission prompts.

```python
#!/usr/bin/env -S uv run --script
# /// script
# dependencies = []
# requires-python = ">=3.11"
# ///

import json
import sys

data = json.load(sys.stdin)
tool_name = data.get("tool_name", "")

# Auto-approve safe read-only tools
safe_tools = ["Read", "Glob", "Grep"]

if tool_name in safe_tools:
    output = {
        "hookSpecificOutput": {
            "hookEventName": "PreToolUse",
            "permissionDecision": "allow",
            "permissionDecisionReason": f"Auto-approved safe tool: {tool_name}"
        }
    }
    print(json.dumps(output))
    sys.exit(0)

# Ask for everything else
output = {
    "hookSpecificOutput": {
        "hookEventName": "PreToolUse",
        "permissionDecision": "ask"
    }
}
print(json.dumps(output))
sys.exit(0)
```

## Advanced: PostToolUse Validation

Validate tool output and provide feedback to Claude.

```python
#!/usr/bin/env -S uv run --script
# /// script
# dependencies = []
# requires-python = ">=3.11"
# ///

import json
import sys
import os

data = json.load(sys.stdin)
tool_name = data.get("tool_name", "")
tool_input = data.get("tool_input", {})

# Example: Check that written files have proper headers
if tool_name == "Write":
    file_path = tool_input.get("file_path", "")
    content = tool_input.get("content", "")

    if file_path.endswith(".py") and not content.startswith("#"):
        output = {
            "decision": "block",
            "reason": "Python files must start with a shebang or comment"
        }
        print(json.dumps(output))
        sys.exit(0)

# Allow by default
output = {"decision": "allow"}
print(json.dumps(output))
sys.exit(0)
```
