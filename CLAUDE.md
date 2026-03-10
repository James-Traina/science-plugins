# science-plugins Marketplace

A personal Claude Code plugin marketplace for mathematical computing, scientific research, and developer productivity.

## What This Repo Is

This is a **marketplace catalog**, not a plugin itself. It contains only:
- `.claude-plugin/marketplace.json` — the machine-readable plugin catalog
- `README.md` — the human-readable plugin listing

Each plugin lives in its own repo (e.g. `James-Traina/wolfram-hart`) and is referenced here by name.

## Plugins

| Plugin | Category | Components | Repo |
|--------|----------|------------|------|
| `wolfram-hart` | science | 1 skill · 3 commands · 1 agent | James-Traina/wolfram-hart |
| `compound-science` | science | 14 skills · 15 commands · 20 agents · 7 hooks | James-Traina/compound-science |
| `claude-codex` | productivity | 1 skill · 2 commands · 1 agent · 2 hooks | James-Traina/claude-codex |
| `ralpha-team` | productivity | 5 agents · 5 commands · 5 hooks | James-Traina/ralpha-team |

## Adding a New Plugin

### 1. Add to marketplace.json

Add an entry to the `"plugins"` array in `.claude-plugin/marketplace.json`:

```json
{
  "name": "plugin-name",
  "description": "One sentence. What it does and why it's useful.",
  "source": {
    "source": "github",
    "repo": "James-Traina/plugin-name"
  },
  "category": "science | productivity",
  "tags": ["tag1", "tag2", "tag3"]
}
```

### 2. Add to README.md

Add a new `### [plugin-name](url)` section before `## Updating`. Follow the existing format:
- 2-paragraph description (what it does, key mechanism)
- `**Components:**` line with counts
- `**Prerequisites:**` line if any external deps are required
- Install code block

Also add `/plugin update plugin-name` to the Updating section.

## Removing a Plugin

Remove the entry from `marketplace.json` and the section from `README.md`. Removing from the catalog does not uninstall the plugin for users who already installed it.

## Plugin Standards

All plugins in this marketplace follow these conventions. When auditing or updating a plugin, verify:

### plugin.json (required fields, in order)
```json
{
  "name": "plugin-name",
  "version": "X.Y.Z",
  "description": "...",
  "author": { "name": "James Traina", "url": "https://github.com/James-Traina" },
  "homepage": "https://github.com/James-Traina/plugin-name",
  "repository": "https://github.com/James-Traina/plugin-name",
  "license": "MIT",
  "keywords": ["tag1", "tag2"]
}
```

### File structure (every plugin)
```
.claude-plugin/plugin.json     Manifest (required)
agents/                        Agent .md files
commands/                      Command .md files
skills/                        Skill directories with SKILL.md
hooks/hooks.json               Hook definitions (if plugin has hooks)
hooks/*.sh                     Hook scripts (if plugin has hooks)
scripts/                       Shared scripts used by hooks and commands
settings.json                  Plugin permissions {"permissions":{"allow":[],"deny":[]}}
CLAUDE.md                      Plugin development guide
README.md                      User-facing docs
LICENSE                        MIT, Copyright (c) 2026 James Traina
CHANGELOG.md                   Keep a Changelog format, SemVer
.gitignore                     Includes .DS_Store *.swp *.swo *~ .serena/ .claude/
.github/workflows/ci.yml       CI: JSON validation + test suite
.tests/                        Hidden test directory
.evals/                        Hidden eval directory (AI-graded, manual)
```

### Agent frontmatter standard
```yaml
---
name: agent-name
description: >-
  Trigger text. Use when... Examples:

  <example>
  Context: ...
  user: "..."
  assistant: "..."
  <commentary>...</commentary>
  </example>
model: sonnet
tools:
  - Read
  - Grep
---
```

### Command frontmatter standard
```yaml
---
name: command-name
description: One-line description
argument-hint: "<required> [optional]"
allowed-tools: Bash, Read
---
```

### Skill frontmatter standard
```yaml
---
name: skill-name
description: >-
  Trigger description. Use when...
---
```

### Shell script standard
- Shebang: `#!/usr/bin/env bash`
- Flags: `set -euo pipefail` (test runners omit `-e` deliberately)
- All scripts must be executable (`chmod +x`)

### hooks.json standard
```json
{
  "description": "plugin-name hooks — purpose",
  "hooks": {
    "EventName": [
      {
        "matcher": "*",
        "hooks": [
          {
            "type": "command",
            "command": "bash \"${CLAUDE_PLUGIN_ROOT}/hooks/script-name.sh\"",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

### CHANGELOG.md standard
```markdown
# Changelog

All notable changes to plugin-name are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/). Versioning follows [Semantic Versioning](https://semver.org/).

## [X.Y.Z] - YYYY-MM-DD
```

## Versioning

Bump `version` in `.claude-plugin/plugin.json` for every release. Claude Code caches plugins — users only receive updates when the version number changes.

| Change type | Version bump |
|-------------|-------------|
| New agent, command, skill, or hook | minor (0.X.0) |
| Bug fix or content improvement | patch (0.0.X) |
| Breaking change (rename, remove) | major (X.0.0) |
