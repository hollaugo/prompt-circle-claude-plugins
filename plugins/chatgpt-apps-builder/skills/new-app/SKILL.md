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

Generate the complete application code. **DO NOT SKIP ANY FILES.**

### MANDATORY FILES CHECKLIST

You MUST create ALL of these files. Widgets will NOT work without them:

#### Root Level (REQUIRED)
- [ ] `package.json` - Server dependencies
- [ ] `tsconfig.json` - TypeScript config
- [ ] `setup.sh` - One-command setup script
- [ ] `START.sh` - Server start script
- [ ] `.env.example` - Environment template

#### Server Files (REQUIRED)
- [ ] `server/index.ts` - MCP server with **StreamableHTTPServerTransport** and resource registration
- [ ] `server/tools/*.ts` - Tool handler files

#### Widget Build Infrastructure (REQUIRED - DO NOT SKIP)
- [ ] `web/package.json` - **CRITICAL**: Must include esbuild, React, Tailwind, Vite
- [ ] `web/build.js` - **CRITICAL**: Widget bundler script
- [ ] `web/tsconfig.json` - TypeScript config for widgets
- [ ] `web/tailwind.config.js` - Tailwind configuration
- [ ] `web/postcss.config.js` - PostCSS configuration
- [ ] `web/vite.config.ts` - Vite config for dev server
- [ ] `web/index.html` - Dev preview page with widget selector
- [ ] `web/src/globals.css` - Tailwind CSS entry point
- [ ] `web/src/hooks.ts` - Apps SDK hooks
- [ ] `web/src/lib/utils.ts` - Utility functions (cn, formatters)
- [ ] `web/src/dev/mock-openai.ts` - Mock window.openai API for local testing

#### Widget Components (REQUIRED for UI)
- [ ] `web/src/widgets/*.tsx` - Widget entry points
- [ ] `web/src/components/ui/*.tsx` - shadcn components used

### Implementation Steps

1. **Create ALL files from checklist above**
   Do NOT proceed until every file is created.

2. **Generate MCP Server**
   Use the `chatgpt-mcp-generator` agent.
   - Server MUST register widgets as resources with `text/html+skybridge`
   - Server MUST import bundles from `./resources/*.js`

3. **Generate Widgets**
   Use the `chatgpt-widget-builder` agent.

4. **CRITICAL: Build Widgets**
   After generating widget source files, ALWAYS run:
   ```bash
   cd web && npm install && npm run build
   ```
   This generates:
   - `web/dist/*.js` - Bundled widget JS
   - `server/resources/*.ts` - HTML bundles for MCP serving

5. **Verify Widget Build**
   Check these files exist:
   ```bash
   ls web/dist/           # Should have *.js files
   ls server/resources/   # Should have *.ts files
   ```
   **If missing, widgets will NOT display in ChatGPT!**

6. **Generate Database Schema** (if needed)
   Use the `chatgpt-schema-designer` agent.

7. **Generate Golden Prompts**
   Create test prompts (5+ direct, 5+ indirect, negative).

### Project Structure

```
{app-name}/
├── package.json              # REQUIRED
├── tsconfig.json             # REQUIRED
├── setup.sh                  # REQUIRED
├── START.sh                  # REQUIRED
├── .env.example              # REQUIRED
├── server/
│   ├── index.ts              # REQUIRED - MCP server
│   ├── tools/                # REQUIRED - Tool handlers
│   └── resources/            # AUTO-GENERATED by build
├── web/
│   ├── package.json          # REQUIRED - DO NOT SKIP
│   ├── build.js              # REQUIRED - DO NOT SKIP
│   ├── vite.config.ts        # REQUIRED - Dev server config
│   ├── index.html            # REQUIRED - Dev preview page
│   ├── tsconfig.json         # REQUIRED
│   ├── tailwind.config.js    # REQUIRED
│   ├── postcss.config.js     # REQUIRED
│   ├── dist/                 # AUTO-GENERATED by build
│   └── src/
│       ├── widgets/          # REQUIRED - Widget entry points
│       ├── components/ui/    # REQUIRED - shadcn components
│       ├── dev/
│       │   └── mock-openai.ts # REQUIRED - Mock API for testing
│       ├── hooks.ts          # REQUIRED - Apps SDK hooks
│       ├── lib/utils.ts      # REQUIRED - Utilities
│       └── globals.css       # REQUIRED - Tailwind CSS
├── supabase/ (optional)
└── .chatgpt-app/
    └── state.json
```

### Dev Preview

After creating widgets, test locally:
```bash
cd web
npm run dev
```

This opens http://localhost:5173 with:
- Widget selector dropdown
- Theme toggle (light/dark)
- Mock data panel
- Hot reload on changes

## Phase 4: Validation & Testing

Before deployment:
1. Run schema validation
2. Check tool annotations
3. Validate widget CSP
4. Run golden prompt tests

## Phase 5: Deployment

Deploy to Render:
1. Generate render.yaml and Dockerfile
2. Deploy via Render MCP
3. Configure environment
4. Verify /mcp endpoint
5. Provide connector URL

## State Persistence

After each phase, save progress to `.chatgpt-app/state.json` for resume capability.

## Getting Started

When invoked, begin with:
"What ChatGPT App would you like to build? Describe what it does and the problem it solves."

Then guide them through each phase systematically.
