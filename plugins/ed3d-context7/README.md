# ed3d Context7 Plugin

Access up-to-date documentation for thousands of frameworks and libraries via the Context7 MCP server.

## Description

This plugin provides access to Context7, which tries to solve the "hallucinated API" problem by giving Claude real-time access to current, version-specific documentation for popular libraries and frameworks.

## Installation

```bash
/plugin install ed3d-context7@ed3d-plugins
```

After installation, restart Claude Code to activate the plugin.

## Configuration

### Free Tier (No API Key)

Context7 works out of the box with no configuration required. The free tier provides access to documentation for thousands of libraries.

### Pro Tier (Optional API Key)

For enhanced features and higher rate limits, you can add an API key to your `.ed3d-plugins.env`:

```bash
# Context7 API key (optional - free tier works without this)
# Get your API key from https://context7.com/
export CONTEXT7_API_KEY="c7_..."
```

**Pro tier benefits**:
- Higher rate limits
- Priority access
- Additional features

## Features

### Massive Library Coverage
Context7 supports thousands of libraries and frameworks including:
- **Frontend**: React, Next.js, Vue, Svelte, Angular
- **Backend**: Node.js, Express, FastAPI, Django, Rails
- **Databases**: PostgreSQL, MongoDB, Redis, Prisma
- **Cloud**: AWS SDK, Google Cloud, Azure
- **Tools**: Vite, Webpack, ESBuild, TypeScript
- And many more...

### Token Efficiency
- 85% reduction in token usage compared to including full docs
- Smart retrieval of only relevant documentation
- Reduces hallucination-related back-and-forth

## Usage

Context7 works automatically when Claude needs documentation. You can also explicitly invoke it:

**Implicit usage** (automatic):
```
Create a Next.js 14 app with server components and routing
```

**Explicit usage**:
```
use context7
Create a Next.js 14 app with server components and routing
```

**Version-specific queries**:
```
Show me how to use React 19 hooks
```

**Framework comparisons**:
```
Compare authentication approaches in Express vs FastAPI
```

## How It Works

1. When Claude encounters a library or framework in your request
2. Context7 automatically fetches current documentation from official sources
3. Claude receives accurate, up-to-date API information
4. You get working code that uses current best practices

## Tools Provided

The Context7 MCP server provides these tools:

- **c7_query** - Query documentation for specific topics
- **c7_search** - Search across library documentation
- **c7_info** - Get information about supported libraries
- **c7_projects_list** - List available documentation projects

## Benefits

### No More Hallucinations
Claude won't make up non-existent functions or use deprecated APIs. All code examples use current, documented methods.

### Always Current
Documentation updates automatically. When a library releases a new version, Context7 provides the latest docs.

### Multi-Library Support
Get documentation for multiple libraries in a single request. Perfect for full-stack development.

### Works Everywhere
Context7 integrates seamlessly with Claude Code. No special commands or syntax needed.

## Use Cases

**Learning New Frameworks**:
```
I'm new to Svelte 5. Show me how to create a reactive component with the new runes syntax.
```

**Migrating Between Versions**:
```
Help me migrate this React 17 component to React 19, using the latest hooks and patterns.
```

**API Integration**:
```
Show me how to upload files to S3 using the latest AWS SDK v3 for JavaScript.
```

**Best Practices**:
```
What's the current recommended way to handle authentication in Next.js 15?
```

## Security

This plugin uses a **clean environment** approach for the MCP server:

- Environment is cleared with `env -i` before starting
- Only necessary variables are passed: `PATH`, `CLAUDE_PROJECT_ROOT`, `CONTEXT7_API_KEY`
- API key is optional and sourced from gitignored `.ed3d-plugins.env` file
- Prevents accidental exposure of environment variables

## Technical Details

- **MCP Package**: `@upstash/context7-mcp` (official Upstash package)
- **Environment**: Isolated with explicit variable passing
- **API Key**: Optional, works without it on free tier
- **Rate Limits**: Free tier has limits, Pro tier has higher limits

## Troubleshooting

### MCP server not starting

Check MCP server status:
```bash
/mcp
```

Verify the plugin is enabled:
```bash
/plugin list
```

### Documentation not loading

Context7 requires internet access to fetch documentation. Ensure you have network connectivity.

### Rate limit errors

If you're hitting rate limits on the free tier:
1. Get a Context7 API key from https://context7.com/
2. Add it to `.ed3d-plugins.env` as `CONTEXT7_API_KEY`
3. Restart Claude Code

### Checking supported libraries

Ask Claude:
```
What libraries does Context7 support?
```

Or use the `c7_info` tool directly.

## Comparison with Other Approaches

**vs. Including full documentation**:
- ✅ 85% less tokens
- ✅ Always current
- ✅ Faster responses

**vs. Claude's training data**:
- ✅ Post-training releases covered
- ✅ Version-specific information
- ✅ No hallucinations about new features

**vs. Manual documentation lookups**:
- ✅ Automatic retrieval
- ✅ Context-aware selection
- ✅ No copy-pasting needed

## License

UNLICENSED
