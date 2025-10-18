# Web Fetch Plugin

Enhanced web content fetching with detailed extraction capabilities using the fetch MCP server.

## Description

This plugin provides a specialized agent for fetching and extracting information from web pages. It supersedes the built-in `WebFetch` tool for tasks requiring detailed content extraction or verbatim passages.

## Installation

```bash
/plugin install web-fetch@ed3d-plugins
```

After installation, restart Claude Code to activate the plugin.

## Features

- **Detailed content extraction**: Fetch specific information from web pages with high precision
- **Verbatim content support**: Retrieve exact quotes, code samples, or passages without modification
- **Incremental fetching**: Handles large pages by fetching content in manageable chunks
- **Haiku-powered**: Uses Claude 3.5 Haiku for fast, efficient content extraction
- **Structured extraction**: Extract API documentation, installation instructions, error messages, and more

## Agent: Web Fetch

The web-fetch agent is automatically available after plugin installation. Claude will invoke it when appropriate, particularly when you need:

- Detailed information from web pages
- Verbatim quotes or code samples
- Specific sections from documentation
- Structured data extraction

### Example Usage

Ask Claude:
- "Get the installation instructions from https://example.com/docs"
- "Extract all code examples from https://tutorial.example.com"
- "What does https://api.example.com/docs say about authentication?"
- "Get the exact error message text from https://errors.example.com/500"

## Configuration

This plugin uses the fetch MCP server (`mcp-server-fetch`) via `uvx`. No additional environment variables are required.

### Requirements

- `uvx` must be installed (comes with `uv`)
- Internet connectivity for web fetching

## How It Works

1. The plugin provides a fetch MCP server that handles web content retrieval
2. The web-fetch agent uses Claude 3.5 Haiku with access to only the fetch MCP tools
3. The agent fetches content starting at 20,000 characters and scales up as needed
4. Content is extracted and formatted according to your specific requirements

## Advantages Over WebFetch

- **More detailed extraction**: Better at finding specific information in large pages
- **Verbatim content**: Preserves exact formatting for quotes, code, and structured data
- **Incremental loading**: Handles large pages more efficiently
- **Specialized focus**: Optimized specifically for content extraction tasks
- **Transparent operation**: Clear indication of what was found and from where

## Technical Details

- **Model**: Claude 3.5 Haiku (fast and efficient)
- **Tools**: Only fetch MCP tools (focused capability set)
- **Default fetch size**: 20,000 characters
- **Scaling**: Automatically requests more content when needed

## Troubleshooting

### Agent not being invoked

If Claude isn't using the web-fetch agent:
- Explicitly ask Claude to use the web-fetch agent
- Try: "Use the web-fetch agent to get [content] from [URL]"

### Fetch MCP server not starting

Ensure `uvx` is installed:
```bash
pip install uv
```

Check MCP server status:
```bash
/mcp
```

### Content not found

The agent will inform you if it cannot find requested content after fetching. This may indicate:
- The content doesn't exist on the page
- The page structure prevents extraction
- The URL is inaccessible

## License

MIT
