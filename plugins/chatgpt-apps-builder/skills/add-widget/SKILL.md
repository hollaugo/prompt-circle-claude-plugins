---
name: chatgpt-app:add-widget
description: Add a new React widget to your ChatGPT App with shadcn/ui components, Apps SDK integration, and accessibility.
---

# Add React Widget

You are helping the user add a new React widget to their ChatGPT App using shadcn/ui components.

## Widget Patterns Available

Choose from these standardized widget patterns:

### 1. List Widget
- Displays a collection of items with search/filter
- Card-based layout with selection state
- Actions: create, refresh, item click
- Best for: task lists, product catalogs, search results

### 2. Detail Widget
- Shows single item with all fields
- Edit/delete capabilities
- Back navigation to list
- Best for: item details, profile views

### 3. Form Widget
- Create or edit items
- Validation with error display
- Success state with redirect
- Best for: data entry, settings

### 4. Chart Widget
- Data visualization with multiple chart types
- Line, bar, area, and pie charts
- Interactive with tooltips
- Best for: analytics, dashboards, reports

### 5. Carousel Widget
- Horizontal scrolling gallery
- Swipe gestures and navigation
- Card previews with click-through
- Best for: product showcases, image galleries

## Workflow

1. **Gather Information**
   Ask the user:
   - What data will this widget display?
   - What actions should users be able to take?
   - Which pattern fits best?

2. **Define Data Shape**
   Create TypeScript interfaces for:
   - Tool output (data from server)
   - Widget state (persisted UI state)
   - Form data (if applicable)

3. **Select Components**
   From shadcn/ui:
   - **Layout**: Card, Tabs
   - **Data Display**: Badge, Table, Progress, Skeleton
   - **Forms**: Button, Input, Textarea, Select, Label
   - **Feedback**: Alert, Toast
   - **Charts**: SimpleLineChart, SimpleBarChart, SimplePieChart

4. **Generate Widget**
   Use the `widget-builder` agent to create:
   ```
   web/src/widgets/{name}.tsx      - Main widget component
   web/src/components/ui/*.tsx     - Any new shadcn components needed
   ```

5. **CRITICAL: Build Widget**
   After generating the widget source, ALWAYS run:
   ```bash
   cd web && npm run build
   ```
   This:
   - Bundles the widget TSX to `web/dist/{name}.js`
   - Generates `server/resources/{name}.ts` with HTML bundle
   - Server resources are AUTO-GENERATED, do NOT create manually!

6. **Verify Build Output**
   ```bash
   # Check these files exist:
   ls web/dist/{name}.js
   ls server/resources/{name}.ts
   ```
   If missing, the widget will NOT display in ChatGPT!

7. **Register Widget in Server**
   Update `server/index.ts` to:
   - Import: `import { {name}Bundle } from "./resources/{name}.js";`
   - Add to RESOURCES array with URI `ui://widget/{name}.html`
   - Link tool's `_meta.openai/outputTemplate` to widget URI

8. **Update State**
   Add widget to `.chatgpt-app/state.json` widgets array.

## shadcn/ui Component Usage

### Button Variants
```tsx
<Button variant="default">Primary</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="outline">Outline</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="destructive">Delete</Button>
```

### Cards
```tsx
<Card>
  <CardHeader>
    <CardTitle>Title</CardTitle>
    <CardDescription>Description</CardDescription>
  </CardHeader>
  <CardContent>Content</CardContent>
  <CardFooter>Footer</CardFooter>
</Card>
```

### Badges
```tsx
<Badge variant="default">Default</Badge>
<Badge variant="success">Success</Badge>
<Badge variant="warning">Warning</Badge>
<Badge variant="destructive">Error</Badge>
```

### Charts
```tsx
<SimpleLineChart
  data={data}
  xAxisKey="date"
  lines={[
    { key: "revenue", name: "Revenue" },
    { key: "cost", name: "Cost" },
  ]}
/>

<SimplePieChart
  data={[
    { name: "Category A", value: 400 },
    { name: "Category B", value: 300 },
  ]}
/>
```

## Apps SDK Hooks

```tsx
// Get data from tool
const toolOutput = useToolOutput<MyDataType>();

// Theme sync (auto-syncs with ChatGPT dark/light mode)
const theme = useTheme();

// Persistent widget state
const [state, setState] = useWidgetState({ selectedId: null });

// Call other tools
const callTool = useCallTool();
await callTool("update-item", { id, data });

// Navigate
const sendFollowUp = useSendFollowUp();
await sendFollowUp("Show the item list");

// Display modes
const requestDisplayMode = useRequestDisplayMode();
await requestDisplayMode("fullscreen");
```

## Display Modes

| Mode | Use Case | Max Height |
|------|----------|------------|
| `inline` | Lists, cards, quick actions | ~400px |
| `fullscreen` | Detail views, forms, complex UIs | Full viewport |
| `pip` | Persistent widgets, live data | Floating |

## File Structure

```
web/
├── src/
│   ├── components/ui/     # shadcn components
│   │   ├── button.tsx
│   │   ├── card.tsx
│   │   ├── badge.tsx
│   │   ├── chart.tsx
│   │   └── index.ts
│   ├── lib/
│   │   └── utils.ts       # cn() helper, formatters
│   ├── widgets/           # Widget entry points
│   │   ├── item-list.tsx
│   │   ├── item-detail.tsx
│   │   └── item-form.tsx
│   ├── hooks.ts           # Apps SDK hooks
│   └── globals.css        # Tailwind + CSS variables
├── build.js               # esbuild config
├── tailwind.config.js
└── package.json
```

## Accessibility Checklist

- [ ] Color contrast WCAG AA (4.5:1 text, 3:1 UI)
- [ ] All interactive elements have focus states
- [ ] Images have alt text
- [ ] Form inputs have labels
- [ ] Loading states announced to screen readers
- [ ] Keyboard navigation works
