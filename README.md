# Prompt Circle Claude Plugins

A collection of Claude Code plugins for AI-assisted development workflows.

## Installation

### Add the Marketplace

```bash
# From GitHub (after publishing)
/plugin marketplace add prompt-circle/prompt-circle-claude-plugins

# From local directory (for development)
/plugin marketplace add /path/to/prompt-circle-claude-plugins
```

### Install a Plugin

```bash
/plugin install chatgpt-apps-builder
```

## Available Plugins

### chatgpt-apps-builder

Build complete ChatGPT Apps with MCP servers, React widgets (shadcn/ui), and cloud deployment to Render.

**Features:**
- Full-stack TypeScript (MCP server + React widgets)
- shadcn/ui components with Tailwind CSS
- Multiple widget patterns: List, Detail, Form, Chart, Carousel
- Optional authentication (Auth0, Supabase Auth)
- Optional database (Supabase PostgreSQL)
- Deployment to Render
- State persistence across sessions

**Skills:**
| Command | Description |
|---------|-------------|
| `/chatgpt-app:new` | Create a new ChatGPT App from concept to working code |
| `/chatgpt-app:resume` | Resume building an in-progress app |
| `/chatgpt-app:add-tool` | Add a new MCP tool |
| `/chatgpt-app:add-widget` | Add a new React widget |
| `/chatgpt-app:add-auth` | Configure authentication |
| `/chatgpt-app:add-database` | Configure Supabase database |
| `/chatgpt-app:validate` | Run validation suite |
| `/chatgpt-app:test` | Run automated tests |
| `/chatgpt-app:deploy` | Deploy to Render |

**Agents:**
- `app-architect` - Conceptualizes apps with UX principles
- `mcp-generator` - Generates TypeScript MCP server code
- `widget-builder` - Creates React widgets with shadcn/ui
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
