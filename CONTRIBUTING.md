# Contributing to ed3d-plugins

### 1. Create Your Plugin

Follow the [Claude Code plugin documentation](https://docs.claude.com/en/docs/claude-code/plugins) to create your plugin. At minimum, your plugin needs:

- A `.claude-plugin/plugin.json` manifest file
- At least one component (slash command, subagent, hook, or MCP server)
- A README.md explaining what the plugin does and how to use it

### 2. Plugin Structure

Your plugin should follow this structure:

```
your-plugin/
├── .claude-plugin/
│   └── plugin.json          # Required: Plugin manifest
├── commands/                # Optional: Slash commands
│   └── *.md
├── agents/                  # Optional: Subagents
│   └── *.md
├── hooks/                   # Optional: Hooks
│   └── hooks.json
├── .mcp.json               # Optional: MCP servers
└── README.md               # Recommended: Documentation
```

**For MCP servers**: See [CLAUDE.md](./CLAUDE.md) for detailed implementation patterns. All ed3d-plugins use a standardized approach:
- Bash wrapper with `.ed3d-plugins.env` sourcing
- **Clean environment pattern** (recommended): use `env -i` to explicitly pass only required variables
- Document required environment variables in your README.md

### 3. Host Your Plugin

Choose one of these options:

**Option A: In this repository (recommended for collaboration)**
1. Fork this repository
2. Create a `plugins/your-plugin-name/` directory
3. Add your plugin files to that directory
4. Submit a pull request

**Option B: In your own repository**
1. Create a separate repository for your plugin
2. Submit a pull request to update `.claude-plugin/marketplace.json` with your plugin's source

**Option C: As a GitHub repository**
1. Host your plugin in any GitHub repository
2. Submit a pull request to add it to the marketplace manifest

### 4. Update the Marketplace Manifest

Add your plugin to `.claude-plugin/marketplace.json`:

```json
{
  "name": "your-plugin-name",
  "source": "./plugins/your-plugin-name",  // or "user/repo" or git URL
  "description": "Brief description of what your plugin does",
  "category": "development",  // Optional: helps with organization
  "tags": ["keyword1", "keyword2"]  // Optional: helps with discovery
}
```

### 5. Submit a Pull Request

1. Ensure your plugin follows the structure above
2. Test your plugin locally before submitting
3. Include a clear description of what your plugin does
4. Reference any relevant issues or discussions

## Plugin Quality Guidelines

- **Documentation**: Include a README.md with installation and usage instructions
- **Versioning**: Use semantic versioning in your plugin.json
- **Testing**: Test your plugin locally before submitting
- **Descriptions**: Write clear, concise descriptions
- **Security**:
  - Do not include sensitive data or credentials in plugin files
  - Use `.ed3d-plugins.env` for API keys and secrets
  - Prefer clean environment pattern (`env -i`) for MCP servers
  - Document all required environment variables in README.md

## Plugin Validation

Before submitting, validate your plugin:

```bash
/plugin validate /path/to/your-plugin
```

## Questions?

- Review [CLAUDE.md](./CLAUDE.md) for comprehensive plugin development guide
- Check existing plugins in `plugins/` for examples (e.g., `ed3d-fetch`, `ed3d-tavily`)
- Review the [Claude Code plugin documentation](https://docs.claude.com/en/docs/claude-code/plugins)
- Open an issue for questions or discussions

## Code of Conduct

Be respectful and constructive in all interactions. We're building a helpful community of developers.
