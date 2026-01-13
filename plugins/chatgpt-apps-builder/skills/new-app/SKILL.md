---
name: chatgpt-app:new
description: Create a new ChatGPT App from concept to working code. Guides through conceptualization, design, implementation, testing, and deployment.
---

# Create a New ChatGPT App

You are helping the user create a new ChatGPT App. Follow this multi-phase workflow to take them from concept to a working, deployable application.

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

Generate the complete application code:

1. **Create project structure**
   ```
   {app-name}/
   ├── package.json
   ├── server/
   │   ├── index.ts
   │   ├── tools/
   │   ├── resources/
   │   ├── auth/ (optional)
   │   └── db/ (optional)
   ├── web/
   │   └── src/
   ├── supabase/ (optional)
   └── .chatgpt-app/
   ```

2. **Generate MCP Server**
   Use the `chatgpt-mcp-generator` agent.

3. **Generate Widgets**
   Use the `chatgpt-widget-builder` agent.

4. **Generate Database Schema**
   Use the `chatgpt-schema-designer` agent (if needed).

5. **Generate Golden Prompts**
   Create test prompts (5+ direct, 5+ indirect, negative).

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
