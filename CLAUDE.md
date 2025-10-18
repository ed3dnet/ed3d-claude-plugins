# Claude Code Plugin Development Guide

## Repository Overview

This repository is a **Claude Code plugin marketplace** for ed3d.net plugins. It distributes plugins that extend Claude Code with custom commands, agents, hooks, and MCP server integrations.

**Repository**: `ed3dnet/ed3d-claude-plugins`
**Marketplace name**: `ed3d-plugins`

## Plugin Architecture

### Standard Plugin Structure

Every plugin follows this directory structure:

```
plugins/plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Required: Plugin manifest
├── commands/                 # Optional: Slash commands
│   └── *.md                 # Markdown files with frontmatter
├── agents/                   # Optional: Subagents
│   └── *.md                 # Agent definitions
├── skills/                   # Optional: Agent Skills
│   ├── skill-name/
│   │   ├── SKILL.md         # Required skill definition
│   │   ├── reference.md     # Optional supporting docs
│   │   └── scripts/         # Optional helper scripts
│   └── another-skill/
│       └── SKILL.md
├── hooks/                    # Optional: Event handlers
│   └── hooks.json
├── .mcp.json                # Optional: MCP server definitions
├── scripts/                  # Optional: Helper scripts for hooks/MCPs
│   └── *.sh, *.py, etc.
├── README.md                # Recommended: Plugin documentation
└── CHANGELOG.md             # Recommended: Version history
```

**Critical**: All component directories (commands/, agents/, skills/, hooks/) must be at the plugin root, NOT inside `.claude-plugin/`.

### Plugin Manifest (plugin.json)

The `.claude-plugin/plugin.json` file is required and defines plugin metadata:

```json
{
  "name": "plugin-name",
  "version": "1.0.0",
  "description": "Brief description of what the plugin does",
  "author": {
    "name": "Author Name",
    "email": "author@example.com",
    "url": "https://github.com/author"
  },
  "homepage": "https://docs.example.com/plugin",
  "repository": "https://github.com/author/plugin",
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"],
  "commands": ["./custom/commands/special.md"],
  "agents": "./custom/agents/",
  "hooks": "./config/hooks.json",
  "mcpServers": "./mcp-config.json"
}
```

**Required fields**: `name`
**Optional but recommended**: `version`, `description`, `author`

**Path behavior**: Custom paths supplement default directories - they don't replace them. If `commands/` exists, it's loaded in addition to custom command paths.

## Environment Variables for MCP Servers

### The Problem

**IMPORTANT BUG**: Environment variable expansion (`${VAR}` syntax) does NOT work in plugin `.mcp.json` files as of Claude Code v2.0.14+. This is a known bug (GitHub issue #9427). The same file works in project root but fails in plugin directories.

### Our Solution: Standardized Environment File

All ed3d-plugins use a **consistent workaround** that provides additional benefits:

**File**: `.claude-plugins.env` (in project root, gitignored)

**Pattern**: All MCP servers in our plugins use this approach:

```json
{
  "mcpServers": {
    "plugin-service": {
      "command": "bash",
      "args": [
        "-c",
        "source ${CLAUDE_PROJECT_ROOT}/.claude-plugins.env && exec npx -y @service/mcp-server"
      ]
    }
  }
}
```

**Why this works**:
1. `bash -c` allows us to run shell commands
2. `source ${CLAUDE_PROJECT_ROOT}/.claude-plugins.env` loads environment variables
3. `exec` replaces the shell with the actual MCP server process
4. `${CLAUDE_PROJECT_ROOT}` expands correctly (it's a Claude Code variable, not user env var)

**Benefits**:
- **Plugin enable/disable = mode switching**: Disabling plugin stops its MCP servers
- **Secure secrets**: API keys stay in gitignored file
- **Password manager integration**: Can source from 1Password, Bitwarden, etc.
- **Per-project configuration**: Different projects can have different credentials
- **Works around the bug**: Avoids broken `${VAR}` expansion in plugin `.mcp.json`

### Environment File Structure

Users create `.claude-plugins.env` in their project root:

```bash
# .claude-plugins.env
export OPENAI_API_KEY="sk-..."
export ANTHROPIC_API_KEY="sk-ant-..."
export GITHUB_TOKEN="ghp_..."

# Password manager integration examples:
export GITHUB_TOKEN=$(op read "op://Private/GitHub/token")
export OPENAI_API_KEY=$(bw get password "OpenAI API Key")
```

**Template provided**: `.claude-plugins.env.example` in repository root

## Available Plugin Components

### 1. Commands (Slash Commands)

**Location**: `commands/` directory
**Format**: Markdown files with frontmatter

```markdown
---
description: Brief description of what this command does
---

# Command Implementation

The actual prompt/instructions that Claude will execute when this command is invoked.
```

**Invocation**: Users type `/command-name` (filename without .md extension)

**Namespacing**: Plugin commands are namespaced by plugin name to avoid conflicts.

### 2. Agents (Subagents)

**Location**: `agents/` directory
**Format**: Markdown files describing agent capabilities

```markdown
---
description: What this agent specializes in
capabilities: ["task1", "task2", "task3"]
---

# Agent Name

Detailed description of the agent's role, expertise, and when Claude should invoke it.

## Capabilities
- Specific task the agent excels at
- Another specialized capability
- When to use this agent vs others

## Context and examples
Provide examples of when this agent should be used.
```

**Behavior**: Claude autonomously invokes agents based on task context. Users can also invoke manually.

### 3. Skills (Agent Skills)

**Location**: `skills/` directory with subdirectories
**Format**: Each skill is a directory containing `SKILL.md`

```
skills/
├── skill-name/
│   ├── SKILL.md          # Required
│   ├── reference.md      # Optional
│   └── scripts/          # Optional
└── another-skill/
    └── SKILL.md
```

**SKILL.md format**: See [Agent Skills documentation](https://docs.claude.com/en/docs/claude-code/skills)

**Behavior**: Claude autonomously invokes skills based on matching task context. This is model-invoked, not user-invoked.

### 4. Hooks (Event Handlers)

**Location**: `hooks/hooks.json` in plugin root
**Format**: JSON configuration with event matchers and actions

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/format-code.sh"
          }
        ]
      }
    ],
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh"
          }
        ]
      }
    ]
  }
}
```

**Available events**:
- `PreToolUse`: Before Claude uses any tool
- `PostToolUse`: After Claude uses any tool
- `UserPromptSubmit`: When user submits a prompt
- `Notification`: When Claude Code sends notifications
- `Stop`: When Claude attempts to stop
- `SubagentStop`: When a subagent attempts to stop
- `SessionStart`: At beginning of sessions
- `SessionEnd`: At end of sessions
- `PreCompact`: Before conversation history is compacted

**Hook types**:
- `command`: Execute shell commands or scripts
- `validation`: Validate file contents or project state
- `notification`: Send alerts or status updates

**Important**: Use `${CLAUDE_PLUGIN_ROOT}` for all plugin-relative paths.

### 5. MCP Servers

**Location**: `.mcp.json` in plugin root or inline in `plugin.json`
**Format**: Standard MCP server configuration with ed3d-plugins pattern

**Standard pattern for ed3d-plugins**:

```json
{
  "mcpServers": {
    "plugin-service-name": {
      "command": "bash",
      "args": [
        "-c",
        "source ${CLAUDE_PROJECT_ROOT}/.claude-plugins.env && exec npx -y @service/mcp-server"
      ]
    },
    "plugin-python-service": {
      "command": "bash",
      "args": [
        "-c",
        "source ${CLAUDE_PROJECT_ROOT}/.claude-plugins.env && exec python3 ${CLAUDE_PLUGIN_ROOT}/servers/service.py"
      ]
    }
  }
}
```

**Critical pattern elements**:
1. Always use `bash -c` wrapper
2. Always source `.claude-plugins.env` from `${CLAUDE_PROJECT_ROOT}`
3. Use `exec` to replace shell with actual process
4. Use `${CLAUDE_PLUGIN_ROOT}` for plugin-relative paths

**Environment variables needed**: Document in plugin README which variables are required (e.g., `OPENAI_API_KEY`, `SERVICE_TOKEN`, etc.)

## Marketplace Configuration

The marketplace is defined in `.claude-plugin/marketplace.json`:

```json
{
  "name": "ed3d-claude-plugins",
  "metadata": {
    "description": "ed3d.net's collection of Claude Code plugins",
    "version": "1.0.0"
  },
  "owner": {
    "name": "Ed Ropple <ed+claudeplugins@ed3d.net>"
  },
  "plugins": [
    {
      "name": "plugin-name",
      "source": "./plugins/plugin-name",
      "description": "Brief description",
      "category": "development",
      "tags": ["keyword1", "keyword2"]
    }
  ]
}
```

**Adding a new plugin**:
1. Create plugin directory in `plugins/`
2. Add entry to `plugins` array in `marketplace.json`
3. Update marketplace version if needed

## Plugin Development Workflow

### 1. Create Plugin Structure

```bash
mkdir -p plugins/my-plugin/.claude-plugin
mkdir -p plugins/my-plugin/commands
mkdir -p plugins/my-plugin/agents
mkdir -p plugins/my-plugin/skills
mkdir -p plugins/my-plugin/hooks
mkdir -p plugins/my-plugin/scripts
```

### 2. Create Plugin Manifest

Create `plugins/my-plugin/.claude-plugin/plugin.json`:

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "What this plugin does",
  "author": {
    "name": "Your Name",
    "email": "email@example.com"
  }
}
```

### 3. Add Components

Add commands, agents, skills, hooks, or MCP servers as needed.

**For MCP servers**: Always use the standard ed3d-plugins pattern with bash wrapper and `.claude-plugins.env` sourcing.

### 4. Document Environment Variables

If your plugin needs environment variables, document them in the plugin's README.md:

```markdown
## Required Environment Variables

Add these to your `.claude-plugins.env` file:

```bash
export MY_PLUGIN_API_KEY="your-key-here"
export MY_PLUGIN_BASE_URL="https://api.example.com"
```
\`\`\`
```

### 5. Register in Marketplace

Add to `.claude-plugin/marketplace.json`:

```json
{
  "name": "my-plugin",
  "source": "./plugins/my-plugin",
  "description": "Brief description of what this plugin does",
  "category": "development",
  "tags": ["relevant", "keywords"]
}
```

### 6. Test Locally

```bash
# From repository root
claude

# Add marketplace
/plugin marketplace add .

# Install your plugin
/plugin install my-plugin@ed3d-claude-plugins

# Test functionality
/my-command        # If you added commands
/mcp              # To check MCP servers
```

### 7. Create Documentation

Create `plugins/my-plugin/README.md`:

```markdown
# My Plugin

Brief description of what the plugin does.

## Installation

```bash
/plugin install my-plugin@ed3d-plugins
```

## Configuration

Add to your `.claude-plugins.env`:

```bash
export MY_PLUGIN_API_KEY="your-key"
```

## Features

- Feature 1
- Feature 2

## Commands

- `/my-command` - Description

## MCP Servers

- `my-plugin-service` - Description

## Usage Examples

Provide examples of how to use the plugin.
```

## Important Environment Variables

### Claude Code Provides

- `${CLAUDE_PLUGIN_ROOT}`: Absolute path to plugin directory
- `${CLAUDE_PROJECT_ROOT}`: Absolute path to project root
- `${CLAUDE_USER_HOME}`: User's home directory

### Our Convention

- `.claude-plugins.env`: User-defined environment variables in project root
  - Must be sourced by MCP servers using bash wrapper pattern
  - Should be gitignored
  - Template provided in `.claude-plugins.env.example`

## Common Patterns

### MCP Server with API Key

```json
{
  "mcpServers": {
    "my-api": {
      "command": "bash",
      "args": [
        "-c",
        "source ${CLAUDE_PROJECT_ROOT}/.claude-plugins.env && exec npx -y @company/mcp-server --api-key \"$MY_API_KEY\""
      ]
    }
  }
}
```

### Hook Script with Plugin Files

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/process.sh"
          }
        ]
      }
    ]
  }
}
```

### Skill with Supporting Scripts

```
skills/
└── my-skill/
    ├── SKILL.md
    ├── reference.md
    └── scripts/
        └── helper.py
```

Reference in SKILL.md: `${CLAUDE_PLUGIN_ROOT}/skills/my-skill/scripts/helper.py`

## Debugging

### Check Plugin Loading

```bash
claude --debug
```

Shows:
- Which plugins are being loaded
- Any errors in plugin manifests
- Command, agent, and hook registration
- MCP server initialization

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Plugin not loading | Invalid `plugin.json` | Validate JSON syntax |
| Commands not appearing | Wrong directory structure | Ensure `commands/` at plugin root |
| Hooks not firing | Script not executable | `chmod +x script.sh` |
| MCP server fails | Missing environment file | Check `.claude-plugins.env` exists |
| MCP env vars not working | Not using bash wrapper | Use standard ed3d-plugins pattern |
| Path errors | Absolute paths used | Use `${CLAUDE_PLUGIN_ROOT}` variable |

### MCP Server Testing

```bash
# Check MCP server status
/mcp

# List configured servers
claude mcp list

# Get specific server details
claude mcp get my-plugin-service
```

## Version Management

Follow semantic versioning:
- **MAJOR**: Breaking changes
- **MINOR**: New features, backward compatible
- **PATCH**: Bug fixes

Update version in:
1. `plugin.json`
2. Plugin's `CHANGELOG.md`
3. Marketplace `marketplace.json` (if needed)

## Distribution

Plugins are distributed via this marketplace repository:

**Installation by users**:
```bash
/plugin marketplace add ed3dnet/ed3d-claude-plugins
/plugin install plugin-name@ed3d-plugins
```

**Plugin location after install**: Plugins are installed to Claude Code's plugin directory, not the project.

**Enable/disable**: Users can enable/disable plugins, which controls whether their MCP servers are active (our "mode switching" feature).

## References

- [Claude Code Plugin Documentation](https://docs.claude.com/en/docs/claude-code/plugins)
- [Claude Code Plugin Reference](https://docs.claude.com/en/docs/claude-code/plugins-reference)
- [Plugin Marketplaces Guide](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)
- [Slash Commands](https://docs.claude.com/en/docs/claude-code/slash-commands)
- [Hooks Reference](https://docs.claude.com/en/docs/claude-code/hooks)
- [MCP Documentation](https://docs.claude.com/en/docs/claude-code/mcp)
- [Agent Skills](https://docs.claude.com/en/docs/claude-code/skills)

## Known Issues & Workarounds

### Environment Variables in Plugin .mcp.json (Bug #9427)

**Issue**: `${VAR}` expansion doesn't work in plugin `.mcp.json` files
**Status**: Open bug as of v2.0.14+
**Workaround**: Use bash wrapper pattern with `.claude-plugins.env` sourcing (documented above)

### Windows Native (Not WSL)

**Issue**: `npx` requires `cmd /c` wrapper on native Windows
**Solution**:
```json
{
  "command": "cmd",
  "args": ["/c", "npx", "-y", "@package/name"]
}
```

## Development Philosophy

### Security First
- Never hardcode API keys or tokens
- Always gitignore `.claude-plugins.env`
- Document required environment variables clearly

### User Experience
- Plugins should work out of the box after env setup
- Clear error messages when environment variables missing
- Comprehensive README for each plugin

### Consistency
- All plugins follow the same directory structure
- All MCP servers use the same bash wrapper pattern
- Environment variables follow consistent naming conventions

### Mode Switching
- Leverage plugin enable/disable for controlling functionality
- MCP servers in plugins become "modes" that can be toggled
- Document which MCP servers provide which capabilities
