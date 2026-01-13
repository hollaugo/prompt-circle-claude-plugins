---
name: chatgpt-app:new
description: Create a new ChatGPT App from concept to working code. Guides through conceptualization, design, implementation, testing, and deployment.
---

# Create a New ChatGPT App

You are helping the user create a new ChatGPT App. Follow this multi-phase workflow to take them from concept to a working, deployable application.

## CRITICAL: HTTP Transport Required

**ChatGPT Apps MUST use Streamable HTTP transport, NOT stdio.**

- Server must use `StreamableHTTPServerTransport` from `@modelcontextprotocol/sdk/server/streamableHttp.js`
- Server must expose `/mcp` endpoint for ChatGPT connector
- Server must expose `/health` endpoint for health checks
- `HTTP_MODE=true` must be set (default in START.sh)
- Stdio mode is ONLY for local testing with MCP Inspector

## Two Implementation Patterns

### Pattern A: Simple Inline (RECOMMENDED)
- All code in `server/index.ts` with inline widget HTML
- Widget HTML generated via template literal function
- Best for: 1-5 tools, simple widgets, quick development
- **This pattern is proven to work reliably with ChatGPT Apps**

### Pattern B: Complex Build Pipeline
- Separate files for tools, React components, build scripts
- Requires `web/build.js` to bundle React widgets
- Best for: Large apps with complex interactive widgets

**Default to Pattern A** unless the user explicitly requests React components.

## Phase 1: Conceptualization

Start by gathering information about the app:

1. **Ask for the app idea**
   "What ChatGPT App would you like to build? Describe what it does and the problem it solves."

2. **Analyze against UX Principles**
   Evaluate the idea against the three pillars:
   - **Conversational Leverage**: What can users accomplish through natural language that would be harder in a traditional UI?
   - **Native Fit**: How does this integrate naturally with ChatGPT's conversational flow?
   - **Composability**: Can the tools work independently and combine with other apps?

3. **Check for Anti-Patterns**
   Warn if the idea includes:
   - Static website content display
   - Complex multi-step workflows requiring external tabs
   - Duplicating ChatGPT's native capabilities
   - Ads or upsells

4. **Define Use Cases**
   Create 3-5 primary use cases with user stories.

5. **Create Value Proposition**
   Document why this app adds value in ChatGPT.

## Phase 2: Design

Design the technical architecture:

1. **Tool Topology**
   Define the MCP tools needed:
   - Query tools (readOnly: true)
   - Mutation tools (destructive: false)
   - Destructive tools (destructive: true)
   - Widget tools (return UI)
   - External API tools (openWorld: true)

2. **Widget Patterns**
   Determine which widgets are needed:
   - List, Detail, Form, Carousel, Fullscreen

3. **Data Model**
   Design entities and relationships.

4. **Auth Requirements**
   - Single-user (no auth, uses openai/subject)
   - Multi-user (Auth0 or Supabase Auth)

5. **Database Requirements**
   - No database (stateless)
   - Supabase PostgreSQL (persistent)

## Phase 3: Implementation

Generate the complete application code using **Pattern A** (simple inline) by default.

### PATTERN A FILES (Default - Recommended)

Use templates from `templates/pattern-a/`:

```
{app-name}/
├── package.json              # Server dependencies (templates/pattern-a/package.json.hbs)
├── tsconfig.server.json      # Server TypeScript config (templates/pattern-a/tsconfig.server.json.hbs)
├── setup.sh                  # One-command setup (templates/pattern-a/setup.sh.hbs)
├── START.sh                  # Server launcher (templates/pattern-a/START.sh.hbs)
├── .env.example              # Environment template (templates/pattern-a/.env.example.hbs)
├── .gitignore                # Git ignores (templates/pattern-a/.gitignore.hbs)
└── server/
    └── index.ts              # Complete MCP server with inline widgets (templates/pattern-a/server/index.ts.hbs)
```

### Pattern A Key Features

1. **Widget Preview Endpoint**
   - `GET /preview` - Lists all widgets with links to preview each
   - `GET /preview/:widgetId` - Shows widget with mock data for local testing
   - No need to connect to ChatGPT to test widgets

2. **Verbose Development Logging**
   - `npm run dev` uses `--clear-screen=false` to preserve logs
   - All MCP requests logged with timestamps in development mode
   - `NODE_ENV=development` enables debug output

3. **Proper .env Handling**
   - `setup.sh` creates `.env` with sensible defaults
   - Won't overwrite existing `.env` without confirmation
   - Creates `.env.local.example` for personal overrides

4. **Multi-Mode Launcher**
   - `./START.sh` - HTTP mode for ChatGPT
   - `./START.sh --dev` - Development with hot reload + logs
   - `./START.sh --preview` - Opens widget preview in browser
   - `./START.sh --stdio` - For MCP Inspector testing

### Pattern A Implementation Steps

1. **Generate files from templates**
   Use the Handlebars templates in `templates/pattern-a/` with these variables:
   - `{{appName}}` - Display name (e.g., "My Calculator")
   - `{{kebabCase appName}}` - Package name (e.g., "my-calculator")
   - `{{appDescription}}` - Short description
   - `{{widgets}}` - Array of widget configurations
   - `{{tools}}` - Array of tool definitions

2. **Widget Configuration**
   Each widget needs:
   ```javascript
   {
     id: "widget-id",
     name: "Widget Name",
     description: "What this widget displays",
     templateUri: "ui://widget/widget-id.html",
     invoking: "Loading...",
     invoked: "Ready",
     mockData: { /* sample data for preview */ }
   }
   ```

3. **Tool-Widget Binding**
   Tools that produce widgets need:
   - `widgetId` property linking to widget config
   - `_meta` in tool definition with `openai/outputTemplate`
   - `structuredContent` in response (becomes `window.openai.toolOutput`)
   - `_meta` in response with `openai/outputTemplate`

4. **Make scripts executable and test**
   ```bash
   chmod +x setup.sh START.sh
   ./setup.sh
   ./START.sh --dev          # See verbose logs
   open http://localhost:3000/preview   # Preview widgets
   ```

### PATTERN B FILES (Only if requested)

Only use Pattern B if user explicitly requests React components or complex widgets.

Additional files for Pattern B:
```
web/
├── package.json          # Widget dependencies
├── build.js              # esbuild bundler
├── tsconfig.json         # Widget TypeScript config
├── tailwind.config.js    # Tailwind config
├── postcss.config.js     # PostCSS config
├── vite.config.ts        # Dev server
├── index.html            # Dev preview
└── src/
    ├── widgets/*.tsx     # React widget components
    ├── components/ui/    # shadcn components
    ├── hooks.ts          # Apps SDK hooks
    ├── lib/utils.ts      # Utilities
    └── globals.css       # Tailwind CSS
```

## Phase 4: Validation & Testing

Before deployment:
1. Test server starts without errors: `./START.sh`
2. Test health endpoint: `curl http://localhost:3000/health`
3. Test MCP endpoint initialization
4. Run golden prompt tests

## Phase 5: Deployment

Deploy to Render:
1. Generate `render.yaml` and `Dockerfile`
2. Deploy via Render
3. Configure environment variables
4. Verify `/mcp` endpoint works
5. Provide ChatGPT connector URL

## State Persistence

After each phase, save progress to `.chatgpt-app/state.json` for resume capability.

## Getting Started

When invoked, begin with:
"What ChatGPT App would you like to build? Describe what it does and the problem it solves."

Then guide them through each phase systematically, using **Pattern A by default**.
