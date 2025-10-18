# ed3d Brave Plugin

Comprehensive search capabilities using Brave Search API with support for web, local, video, image, and news searches, plus AI-powered summarization.

## Description

This plugin provides a specialized search agent powered by Brave Search's comprehensive API. It supersedes the built-in `WebSearch` tool for tasks requiring specialized search types, advanced filtering, or AI summarization capabilities.

## Installation

```bash
/plugin install ed3d-brave@ed3d-claude-plugins
```

After installation, restart Claude Code to activate the plugin.

## Configuration

### Required Environment Variables

Add to your `.ed3d-plugins.env` file in the project root:

```bash
export BRAVE_API_KEY="BSA..."
```

Get your API key from [Brave Search API](https://brave.com/search/api/).

### Password Manager Integration

You can source the API key from a password manager:

```bash
# 1Password
export BRAVE_API_KEY=$(op read "op://Private/Brave/api-key")

# Bitwarden
export BRAVE_API_KEY=$(bw get password "Brave Search API Key")
```

## Features

### Web Search
- Comprehensive web searches with rich result types
- Advanced filtering by country, language, time range, safety level
- Query highlighting and spell checking
- Custom re-ranking with Goggles
- AI summary key generation

### Local Search
- Search for businesses, restaurants, and services
- Detailed information: ratings, hours, AI descriptions
- **Note**: Full capabilities require Pro plan; free tier falls back to web search

### Video Search
- Find video content with comprehensive metadata
- Thumbnail information for previews
- Time range and safety filtering

### Image Search
- Search for images with automatic base64 encoding
- Images ready for direct display
- Safety filtering and spell checking

### News Search
- Current news articles with freshness controls
- Breaking news indicators
- Default freshness: last 24 hours

### AI Summarization
- Generate AI-powered summaries from web search results
- Entity information and inline source references
- Two-step process: web search with summary key, then summarize

## Agent: brave-searcher

The brave-searcher agent is automatically available after plugin installation. Claude will invoke it when appropriate, particularly when you need:

- Comprehensive web searches
- Specialized content types (video, image, news, local)
- Advanced filtering and result customization
- AI-powered summarization of search results

### Example Usage

Ask Claude:
- "Search for recent developments in renewable energy"
- "Find pizza restaurants in downtown Seattle"
- "Show me tutorial videos about machine learning"
- "Find diagrams of neural network architectures"
- "What's the latest news about space exploration?"
- "Summarize what the web says about quantum computing"

## Free Tier vs Pro Plan

### Free Tier (Default)
The agent defaults to free tier parameters and will work with any Brave Search API key:
- Web search: ✅ Full support
- Video search: ✅ Full support
- Image search: ✅ Full support
- News search: ✅ Full support
- Local search: ⚠️ Falls back to web search
- Summarization: ✅ Full support
- Extra snippets: ❌ Not available

### Pro Plan Features
If you have a Pro plan, you can explicitly request:
- `extra_snippets` for additional result excerpts
- Full local search capabilities with detailed business information

Tell the agent you have a Pro plan to enable these features.

## Tools Provided

The Brave Search MCP server provides these tools to the agent:

- **brave_web_search**: Comprehensive web searches with advanced filtering
- **brave_local_search**: Local business and place search
- **brave_video_search**: Video content search with metadata
- **brave_image_search**: Image search with base64 encoding
- **brave_news_search**: News article search with freshness controls
- **brave_summarizer**: AI-powered summarization from search results

## How It Works

1. The plugin provides a Brave Search MCP server with secure environment isolation
2. The brave-searcher agent uses Claude Haiku 4.5 with access only to Brave Search tools
3. The agent selects appropriate search types and parameters based on your query
4. Results are presented with proper source citations and context

## Advantages Over WebSearch

- **Multiple search types**: Specialized searches for web, local, video, image, news
- **Advanced filtering**: Time range, country, language, safety level controls
- **AI summarization**: Generate concise summaries from multiple sources
- **Rich metadata**: Thumbnails, ratings, hours, and detailed information
- **Image encoding**: Base64-encoded images ready for direct display
- **Better citations**: Clear source attribution with URLs
- **News freshness**: Dedicated news search with breaking news indicators

## Security

This plugin uses a **clean environment** approach for the MCP server:

- Environment is cleared with `env -i` before starting
- Only necessary variables are passed: `PATH`, `CLAUDE_PROJECT_ROOT`, `BRAVE_API_KEY`
- Prevents accidental exposure of other environment variables
- API key is sourced from gitignored `.ed3d-plugins.env` file

## Technical Details

- **Model**: Claude Haiku 4.5 (fast and efficient)
- **Tools**: Only Brave Search MCP tools (focused capability set)
- **MCP Package**: `@brave/brave-search-mcp-server@2.0.57`
- **Environment**: Isolated with explicit variable passing

## Troubleshooting

### Agent not being invoked

If Claude isn't using the brave-searcher agent:
- Explicitly ask Claude to use the brave-searcher agent
- Try: "Use the brave-searcher agent to search for [topic]"

### MCP server not starting

Ensure `BRAVE_API_KEY` is set:
```bash
# Check your .ed3d-plugins.env file
cat .ed3d-plugins.env | grep BRAVE
```

Verify the file exists and is sourced properly.

Check MCP server status:
```bash
/mcp
```

### API key issues

- Verify your API key is valid at [Brave Search API](https://brave.com/search/api/)
- Ensure the key is properly exported in `.ed3d-plugins.env`
- Check for extra spaces or quotes around the key value

### Local search returns web results

This is normal on free tier - local search falls back to web search. The agent will inform you when this happens. Upgrade to Pro plan for full local search capabilities.

### Pagination limits

- Most searches have a maximum offset of 9 (10 pages)
- Results per page vary by search type (see documentation)
- The agent will inform you if you've reached pagination limits

## License

GPL-3.0
