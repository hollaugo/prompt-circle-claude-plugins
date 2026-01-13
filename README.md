# Prompt Circle Claude Plugins

A collection of Claude Code plugins for AI-assisted development workflows.

## Installation

### Add the Marketplace

```bash
# From GitHub
/plugin marketplace add hollaugo/prompt-circle-claude-plugins

# From local directory (for development)
/plugin marketplace add /path/to/prompt-circle-claude-plugins
```

### Install a Plugin

```bash
/plugin install chatgpt-apps-builder
```

## Available Plugins

### chatgpt-apps-builder

Build complete ChatGPT Apps with MCP servers, inline widgets, and cloud deployment to Render.

**Features:**
- Single-file architecture (server/index.ts with inline widgets)
- No build pipeline for widgets - pure HTML/CSS/JS
- Widget preview at `/preview` endpoint for local testing
- Session management with StreamableHTTPServerTransport
- Optional authentication (Auth0, Supabase Auth)
- Optional database (Supabase PostgreSQL)
- Deployment to Render

**Skills:**
| Command | Description |
|---------|-------------|
| `/chatgpt-apps-builder:new-app` | Create a new ChatGPT App from concept to working code |
| `/chatgpt-apps-builder:resume-app` | Resume building an in-progress app |
| `/chatgpt-apps-builder:add-tool` | Add a new MCP tool |
| `/chatgpt-apps-builder:add-widget` | Add a new inline widget |
| `/chatgpt-apps-builder:add-auth` | Configure authentication |
| `/chatgpt-apps-builder:add-database` | Configure Supabase database |
| `/chatgpt-apps-builder:validate` | Run validation suite |
| `/chatgpt-apps-builder:test` | Run automated tests |
| `/chatgpt-apps-builder:deploy` | Deploy to Render |

**Agents:**
- `app-architect` - Conceptualizes apps with UX principles
- `mcp-generator` - Generates TypeScript MCP server code
- `widget-builder` - Creates inline HTML/CSS/JS widgets
- `schema-designer` - Designs PostgreSQL schemas
- `auth-configurator` - Configures OAuth flows
- `deploy-orchestrator` - Orchestrates Render deployment
- `test-runner` - Runs MCP Inspector tests

## Development

### Validate the Marketplace

```bash
claude plugin validate /path/to/prompt-circle-claude-plugins
```

### Adding a New Plugin

1. Create a new directory under `plugins/`
2. Add `plugin.json` with skills and agents
3. Register in `.claude-plugin/marketplace.json`

## License

MIT
