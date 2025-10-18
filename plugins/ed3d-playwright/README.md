# ed3d Playwright Plugin

Browser automation and testing capabilities using Microsoft's official Playwright MCP server.

## Description

This plugin provides comprehensive browser automation through Playwright's MCP server. It uses structured accessibility snapshots rather than screenshots, making it fast, lightweight, and LLM-friendly without requiring vision models.

## Installation

```bash
/plugin install ed3d-playwright@ed3d-plugins
```

After installation, restart Claude Code to activate the plugin.

## Configuration

### Optional Configuration

While no configuration is required, you can customize Playwright's behavior via the `.ed3d-plugins.env` file in your project root.

Add to your `.ed3d-plugins.env`:

```bash
# Run in headless mode (no visible browser window)
export PLAYWRIGHT_ARGS="--headless"

# Use a different browser
export PLAYWRIGHT_ARGS="--browser firefox"

# Multiple options (space-separated)
export PLAYWRIGHT_ARGS="--headless --browser webkit --viewport-size 1920x1080"
```

**Common options**:
- `--headless` - Run without visible browser window
- `--browser <name>` - chromium (default), firefox, webkit, chrome, msedge
- `--viewport-size <size>` - Set viewport (e.g., "1280x720")
- `--device <device>` - Emulate device (e.g., "iPhone 15")
- `--user-agent <ua>` - Custom user agent string
- `--isolated` - Don't persist profile to disk

See [Playwright MCP documentation](https://github.com/microsoft/playwright-mcp) for full list of available arguments.

### Browser Profile

By default, Playwright uses a persistent profile that saves login state between sessions:

**Linux**: `~/.cache/ms-playwright/mcp-chrome-profile`
**macOS**: `~/Library/Caches/ms-playwright/mcp-chrome-profile`
**Windows**: `%USERPROFILE%\AppData\Local\ms-playwright\mcp-chrome-profile`

You can delete this directory to clear all stored session data.

## Features

### Core Automation
- **Page navigation**: Navigate to URLs, go back, refresh
- **Element interaction**: Click, type, fill forms, select options, upload files
- **Page inspection**: Accessibility snapshots, screenshots, console messages, network requests
- **JavaScript execution**: Run custom JavaScript in page context
- **Wait operations**: Wait for text, elements, or time delays
- **Browser control**: Resize window, close browser

### Optional Capabilities
- **Tab management**: Create, list, switch, and close tabs
- **PDF generation**: Save pages as PDF documents
- **Coordinate-based actions**: Direct x/y coordinate interactions
- **Tracing**: Record and save Playwright traces for debugging

### Key Advantages
- **Fast and lightweight**: Uses accessibility tree, not pixel-based analysis
- **No vision models needed**: Operates on structured data
- **Deterministic**: Avoids ambiguity of screenshot-based approaches
- **Multi-browser support**: Chromium, Firefox, WebKit

## Tools Provided

The Playwright MCP server provides 40+ tools:

### Navigation
- `browser_navigate` - Navigate to URL
- `browser_navigate_back` - Go back to previous page

### Inspection
- `browser_snapshot` - Get accessibility tree snapshot (preferred for actions)
- `browser_take_screenshot` - Take PNG/JPEG screenshot (visual only)
- `browser_console_messages` - Get console log entries
- `browser_network_requests` - List network requests

### Interaction
- `browser_click` - Click elements
- `browser_type` - Type text into fields
- `browser_fill_form` - Fill multiple form fields at once
- `browser_select_option` - Select dropdown options
- `browser_file_upload` - Upload files
- `browser_press_key` - Press keyboard keys
- `browser_hover` - Hover over elements
- `browser_drag` - Drag and drop between elements

### Execution
- `browser_evaluate` - Execute JavaScript in page context

### Control
- `browser_wait_for` - Wait for text/elements/time
- `browser_resize` - Resize browser window
- `browser_close` - Close browser
- `browser_handle_dialog` - Handle alerts/confirms/prompts

### Advanced (opt-in capabilities)
- `browser_tabs` - Tab management
- `browser_pdf_save` - Save as PDF
- `browser_mouse_click_xy` - Coordinate-based clicking
- `browser_start_tracing` / `browser_stop_tracing` - Trace recording

## How It Works

1. The plugin provides Microsoft's official Playwright MCP server
2. The MCP server runs with a clean, isolated environment for security
3. Playwright interacts with browsers using structured accessibility data
4. All browser state is preserved in a persistent profile by default

## Use Cases

### Direct Tool Usage
Call Playwright tools directly from the main Claude conversation:
- Automate web tasks (form filling, data extraction)
- Test web applications and document behavior
- Capture screenshots and page state
- Debug web pages and inspect network traffic

### Agent-Based Usage

The plugin includes a **playwright-tester agent** for complex testing scenarios.

**When to use the agent**:
- Testing documentation tutorials (the "sandwich test" - verify each step works)
- Structured test scenarios with inputs and expected outputs
- Complex multi-step workflows that would clutter the main conversation
- Visual regression testing across viewports
- Validating web application behavior against specifications

**How it works**:
1. Provide the agent with a URL, steps to perform, and expected outcomes
2. Agent executes automation, captures screenshots to `./tmp/`
3. Agent reports detailed findings with evidence (screenshot paths)
4. You can then view specific screenshots from the main conversation

**Example invocation**:
```
Use the playwright-tester agent to test the login flow at https://app.example.com:
- Navigate to the site
- Fill login form with email: test@example.com, password: demo123
- Click login button
- Expected: Redirect to /dashboard with welcome message
- Take screenshots at each step
```

The agent provides isolated context and detailed reporting without verbose browser interaction logs in your main conversation.

## Security

This plugin uses a **clean environment** approach for the MCP server:

- Environment is cleared with `env -i` before starting
- Only necessary variables are passed: `PATH`, `CLAUDE_PROJECT_ROOT`
- No API keys or secrets required
- Prevents accidental exposure of environment variables

## Technical Details

- **MCP Package**: `@playwright/mcp@latest` (official Microsoft package)
- **Environment**: Isolated with explicit variable passing
- **Profile**: Persistent by default (can be deleted manually)
- **Browsers**: Chromium (default), Firefox, WebKit supported

## Troubleshooting

### Browser not installed

If you get an error about browsers not being installed:
```bash
npx playwright install
```

Or ask Claude to use the `browser_install` tool.

### MCP server not starting

Check MCP server status:
```bash
/mcp
```

Verify the plugin is enabled:
```bash
/plugin list
```

### Clean browser state

Delete the persistent profile directory to start fresh (see Browser Profile section above for location).

### Headed vs Headless

By default, Playwright runs in headed mode (browser window visible). This works best when you have a display. For headless operation, set `PLAYWRIGHT_ARGS="--headless"` in your `.ed3d-plugins.env` file.

## License

UNLICENSED
