# Craft

A Claude Code plugin providing skills for software development best practices.

## Overview

This plugin offers curated skills that guide Claude through proven software development workflows including test-driven development, architectural patterns, refactoring techniques, and AI development practices.

## Installation

### From GitHub

In Claude Code, add the marketplace and install the plugin:

```
/plugin marketplace add ericjohnolson/craft
/plugin install build
```

### From Local Directory

1. Clone the repository:
   ```bash
   git clone https://github.com/ericjohnolson/craft
   ```

2. In Claude Code, add the marketplace and install:
   ```
   /plugin marketplace add /path/to/craft
   /plugin install build@craft
   ```

### Updating

To get the latest version:

```
/plugin marketplace update craft
```

After installation, skills become available as `build:skill-name` and are automatically triggered when relevant to your task.

## Available Skills

