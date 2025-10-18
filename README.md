# ed3d-plugins

## Installation

Add this marketplace to Claude Code:

```bash
/plugin marketplace add ed3dnet/claude-plugins
```

## Configuration

### Environment Variables for MCP Servers

Many plugins in this marketplace include MCP servers that require API keys or authentication tokens. Due to current limitations in Claude Code's plugin system, we use a standardized approach for managing these credentials.

**Setup:**

1. Create a file named `.claude-plugins.env` in your project root (this should be gitignored):

```bash
# .claude-plugins.env
# Environment variables for ed3d-plugins marketplace

# Example: API keys for various services
export OPENAI_API_KEY="sk-..."
export ANTHROPIC_API_KEY="sk-ant-..."
export GITHUB_TOKEN="ghp_..."

# You can also source from password managers like 1Password:
# export GITHUB_TOKEN=$(op read "op://Private/GitHub/token")
```

2. Add `.claude-plugins.env` to your `.gitignore`:

```bash
echo ".claude-plugins.env" >> .gitignore
```

3. Each plugin's MCP servers will automatically source this file before starting, making your environment variables available.

**Why this approach?**

- **Enable/disable modes**: Plugins can be enabled/disabled, controlling which MCP servers are active
- **Secure secrets**: Keep API keys out of your repository
- **Password manager integration**: Source credentials from tools like 1Password, Bitwarden, etc.
- **Per-project configuration**: Different projects can have different credentials
- **Works around bugs**: Avoids current limitations with environment variable expansion in plugin `.mcp.json` files

**Example `.claude-plugins.env` template:**

A template file is provided at `.claude-plugins.env.example`. Copy it to get started:

```bash
cp .claude-plugins.env.example .claude-plugins.env
```

Then edit `.claude-plugins.env` and fill in your actual API keys and tokens.

**Note:** If a plugin requires specific environment variables, check its documentation in the `plugins/` directory for details.

## Available Plugins

Browse available plugins:

```bash
/plugin
```

Then select "Browse Plugins" to see all available plugins from this marketplace.

## Installing Plugins

Install a specific plugin:

```bash
/plugin install <plugin-name>@ed3d-plugins
```

## About

This marketplace serves as a registry for Claude Code plugins. Plugins can extend Claude Code with:

- **Slash commands** - Custom shortcuts for frequently-used operations
- **Subagents** - Specialized agents for specific development tasks
- **Hooks** - Event handlers to customize Claude Code behavior
- **MCP servers** - Integrations with external tools and data sources

## Documentation

For more information about Claude Code plugins:
- [Claude Code Plugin Documentation](https://docs.claude.com/en/docs/claude-code/plugins)
- [Plugin Marketplaces Guide](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)
