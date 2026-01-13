---
name: chatgpt-app:validate
description: Run validation suite on your ChatGPT App to check schemas, annotations, widgets, and UX compliance.
---

# Validate ChatGPT App

You are helping the user validate their ChatGPT App before testing and deployment.

## Validation Checks

### 1. Required Files Check (RUN FIRST)

Check that ALL mandatory files exist. Missing files = widgets won't work!

**Root Files:**
```bash
ls package.json tsconfig.json setup.sh START.sh .env.example
```

**Server Files:**
```bash
ls server/index.ts
ls server/tools/
```

**Widget Build Infrastructure (CRITICAL):**
```bash
ls web/package.json      # MUST EXIST
ls web/build.js          # MUST EXIST
ls web/tsconfig.json     # MUST EXIST
ls web/tailwind.config.js
ls web/postcss.config.js
ls web/src/globals.css
ls web/src/hooks.ts
ls web/src/lib/utils.ts
```

**Widget Sources:**
```bash
ls web/src/widgets/      # Should have *.tsx files
ls web/src/components/ui/ # Should have component files
```

**Build Outputs (after running build):**
```bash
ls web/dist/             # Should have *.js files
ls server/resources/     # Should have *.ts files
```

If ANY of the above are missing, report as **CRITICAL ERROR**.

### 2. Schema Validation
- Each tool has valid JSON Schema
- Required fields marked correctly
- Types are appropriate

### 3. Metadata Validation
- `openai/toolInvocation/invoking` present (max 64 chars)
- `openai/toolInvocation/invoked` present (max 64 chars)
- `openai/outputTemplate` present for widget tools

### 4. Annotation Validation
- Query tools have `readOnlyHint: true`
- Delete tools have `destructiveHint: true`
- External API tools have `openWorldHint: true`

### 5. Widget Build Validation (CRITICAL)
- **Build outputs exist:**
  - `web/dist/*.js` files present (bundled widgets)
  - `server/resources/*.ts` files present (HTML bundles)
- **Server imports match:**
  - Each widget bundle is imported in `server/index.ts`
  - Resource URIs match `ui://widget/{name}.html`
- **Tool connections:**
  - Widget tools have `_meta.openai/outputTemplate`
  - Output template URI matches registered resource

### 6. Widget Content Validation
- MIME type is `text/html+skybridge`
- `widgetDescription` is present
- CSP configuration is valid
- Root element ID matches widget filename pattern

### 7. Database Validation (if enabled)
- All migrations are valid SQL
- Tables have `user_subject` column
- Indexes exist for user queries

### 8. UX Principles Check
- No anti-patterns detected
- Tools are atomic and composable
- UI enhances rather than decorates

## Workflow

1. Load app state from `.chatgpt-app/state.json`
2. Run each validation category
3. Collect errors and warnings
4. Display results
5. Save report to `.chatgpt-app/validation-report.json`

## Results Format

```
## Validation Results

### Schema Validation ✓
All 5 tools have valid schemas.

### Annotation Validation ⚠
- Warning: create-item could be idempotent

### Widget Validation ✓
All 3 widgets validated.

---
**Overall: PASS** (1 warning)
```

## Fix Suggestions

For each error, provide actionable fix with code example.
