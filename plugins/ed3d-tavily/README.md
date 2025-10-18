# ed3d Tavily Plugin

Advanced web search and research capabilities using the Tavily API.

## Description

This plugin provides a comprehensive research agent powered by Tavily's advanced search and data extraction tools. It supersedes the built-in `WebSearch` tool for tasks requiring detailed research, content extraction, website crawling, or structured data gathering.

## Installation

```bash
/plugin install ed3d-tavily@ed3d-claude-plugins
```

After installation, restart Claude Code to activate the plugin.

## Configuration

### Required Environment Variables

Add to your `.ed3d-plugins.env` file in the project root:

```bash
export TAVILY_API_KEY="tvly-..."
```

Get your API key from [Tavily](https://tavily.com/).

### Password Manager Integration

You can source the API key from a password manager:

```bash
# 1Password
export TAVILY_API_KEY=$(op read "op://Private/Tavily/api-key")

# Bitwarden
export TAVILY_API_KEY=$(bw get password "Tavily API Key")
```

## Features

### Advanced Web Search
- Customizable search parameters (depth, time range, domains)
- General and news-specific search modes
- Domain filtering and boosting
- Comprehensive results with extracted content

### Content Extraction
- Extract clean, structured content from URLs
- Support for markdown and text formats
- Handle complex page structures
- Detailed information retrieval

### Website Crawling
- Systematic multi-page data collection
- Configurable depth and breadth limits
- URL pattern filtering
- Category-based filtering (docs, blog, API, etc.)

### Website Mapping
- Discover complete site structure
- Identify navigation paths and hierarchies
- Useful for site audits and content discovery

## Agent: tavily-searcher

The tavily-searcher agent is automatically available after plugin installation. Claude will invoke it when appropriate, particularly when you need:

- Comprehensive web research
- Real-time information gathering
- Content extraction from specific sites
- Website crawling or mapping
- Structured data collection

### Example Usage

Ask Claude:
- "Search for recent developments in AI safety"
- "Extract the API documentation from https://example.com/api"
- "Crawl the blog section of example.com and summarize recent posts"
- "Map the structure of https://docs.example.com"
- "Research X and provide detailed information from multiple sources"

## Tools Provided

The Tavily MCP server provides these tools to the agent:

- **tavily-search**: Advanced web search with filters and ranking
- **tavily-extract**: Clean content extraction from URLs
- **tavily-crawl**: Systematic website crawling
- **tavily-map**: Website structure mapping

## How It Works

1. The plugin provides a Tavily MCP server with secure environment isolation
2. The tavily-searcher agent uses Claude Haiku 4.5 with access only to Tavily tools
3. The agent selects and combines tools based on your research needs
4. Results are synthesized and presented with proper source citations

## Advantages Over WebSearch

- **More comprehensive**: Access to advanced search parameters and filtering
- **Content extraction**: Dedicated tool for extracting clean content from URLs
- **Crawling capability**: Can systematically explore websites for data collection
- **Site mapping**: Discover and understand website structure
- **Specialized focus**: Optimized specifically for research and data gathering tasks
- **Better citations**: Clear source attribution and URL references

## Security

This plugin uses a **clean environment** approach for the MCP server:

- Environment is cleared with `env -i` before starting
- Only necessary variables are passed: `PATH`, `CLAUDE_PROJECT_ROOT`, `TAVILY_API_KEY`
- Prevents accidental exposure of other environment variables
- API key is sourced from gitignored `.ed3d-plugins.env` file

## Technical Details

- **Model**: Claude Haiku 4.5 (fast and efficient)
- **Tools**: Only Tavily MCP tools (focused capability set)
- **MCP Package**: `tavily-mcp@0.2.10`
- **Environment**: Isolated with explicit variable passing

## Troubleshooting

### Agent not being invoked

If Claude isn't using the tavily-searcher agent:
- Explicitly ask Claude to use the tavily-searcher agent
- Try: "Use the tavily-searcher agent to research [topic]"

### MCP server not starting

Ensure `TAVILY_API_KEY` is set:
```bash
# Check your .ed3d-plugins.env file
cat .ed3d-plugins.env | grep TAVILY
```

Verify the file exists and is sourced properly.

Check MCP server status:
```bash
/mcp
```

### API key issues

- Verify your API key is valid at [Tavily](https://tavily.com/)
- Ensure the key is properly exported in `.ed3d-plugins.env`
- Check for extra spaces or quotes around the key value

## License

GPL-3.0
