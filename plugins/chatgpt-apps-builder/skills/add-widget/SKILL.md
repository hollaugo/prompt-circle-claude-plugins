---
name: chatgpt-app:add-widget
description: Add a new inline widget to your ChatGPT App with HTML/CSS/JS and Apps SDK integration.
---

# Add Inline Widget

You are helping the user add a new inline HTML widget to their ChatGPT App.

## Widget Patterns Available

Choose from these standardized widget patterns:

### 1. Card Grid Widget
- Displays items in responsive grid
- Card-based layout with badges
- Best for: task lists, product catalogs, search results

### 2. Stats Dashboard Widget
- Shows key metrics with large numbers
- Color-coded stat boxes
- Best for: analytics, dashboards, summaries

### 3. Table Widget
- Tabular data display
- Sortable columns, hover states
- Best for: data tables, reports, logs

### 4. Bar Chart Widget
- Simple bar visualization
- No external dependencies
- Best for: comparisons, distributions

### 5. Detail Widget
- Shows single item with all fields
- Action buttons
- Best for: item details, confirmations

## Workflow

1. **Gather Information**
   Ask the user:
   - What data will this widget display?
   - What actions should users be able to take?
   - Which pattern fits best?

2. **Define Data Shape**
   Create TypeScript interface for tool output.

3. **Add Widget Config**
   Add to the `widgets` array in `server/index.ts`:
   ```typescript
   {
     id: "my-widget",
     name: "My Widget",
     description: "Displays data visually",
     templateUri: "ui://widget/my-widget.html",
     invoking: "Loading...",
     invoked: "Ready",
     mockData: { /* sample data for preview */ },
   },
   ```

4. **Add Widget HTML**
   Add case to `generateWidgetHtml()` function:
   ```typescript
   if (widgetId === "my-widget") {
     return `<!DOCTYPE html>
   <html lang="en">
   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <title>My Widget</title>
     <style>
       * { box-sizing: border-box; margin: 0; padding: 0; }
       body { font-family: system-ui; padding: 16px; background: #fff; }
       .loading { min-height: 200px; display: flex; align-items: center; justify-content: center; color: #666; }
       /* Widget-specific styles */
     </style>
     ${previewScript}
   </head>
   <body>
     <div id="root"><div class="loading">Loading...</div></div>
     <script>
       (function() {
         let rendered = false;

         function render(data) {
           if (rendered || !data) return;
           rendered = true;
           // Widget rendering logic
           document.getElementById('root').innerHTML = '...';
         }

         function tryRender() {
           if (window.PREVIEW_DATA) { render(window.PREVIEW_DATA); return; }
           if (window.openai?.toolOutput) { render(window.openai.toolOutput); }
         }

         window.addEventListener('openai:set_globals', tryRender);
         const poll = setInterval(() => {
           if (window.openai?.toolOutput || window.PREVIEW_DATA) { tryRender(); clearInterval(poll); }
         }, 100);
         setTimeout(() => clearInterval(poll), 10000);
         tryRender();
       })();
     </script>
   </body>
   </html>`;
   }
   ```

5. **Create/Update Tool**
   Add tool that returns widget data:
   ```typescript
   {
     name: "show_my_widget",
     description: "Display data in My Widget",
     inputSchema: { ... },
     annotations: { title: "Show Widget", readOnlyHint: true },
     widgetId: "my-widget",  // Links to widget config
     execute: (args) => {
       return { /* data that widget will display */ };
     },
   },
   ```

6. **Test Widget**
   ```bash
   npm run dev
   open http://localhost:3000/preview/my-widget
   ```

## Widget HTML Patterns

### Card Grid
```javascript
function render(data) {
  if (rendered || !data?.items) return;
  rendered = true;
  document.getElementById('root').innerHTML = '<div class="grid">' +
    data.items.map(item => `
      <div class="card">
        <div class="title">${item.title}</div>
        <div class="desc">${item.description || ''}</div>
        <span class="badge ${item.status === 'active' ? 'badge-green' : 'badge-yellow'}">
          ${item.status}
        </span>
      </div>
    `).join('') + '</div>';
}
```

### Stats Dashboard
```javascript
function render(data) {
  if (rendered || !data?.stats) return;
  rendered = true;
  document.getElementById('root').innerHTML = '<div class="stats">' +
    data.stats.map(stat => `
      <div class="stat">
        <div class="value">${formatNumber(stat.value)}</div>
        <div class="label">${stat.label}</div>
      </div>
    `).join('') + '</div>';
}
```

### Table
```javascript
function render(data) {
  if (rendered || !data?.rows || !data?.columns) return;
  rendered = true;
  const header = '<thead><tr>' + data.columns.map(col =>
    `<th>${col.label}</th>`
  ).join('') + '</tr></thead>';
  const body = '<tbody>' + data.rows.map(row =>
    '<tr>' + data.columns.map(col =>
      `<td>${row[col.key] ?? '-'}</td>`
    ).join('') + '</tr>'
  ).join('') + '</tbody>';
  document.getElementById('root').innerHTML = '<table>' + header + body + '</table>';
}
```

### Bar Chart
```javascript
function render(data) {
  if (rendered || !data?.bars) return;
  rendered = true;
  const max = Math.max(...data.bars.map(b => b.value));
  document.getElementById('root').innerHTML = '<div class="chart"><div class="bar-container">' +
    data.bars.map(bar => {
      const height = max > 0 ? (bar.value / max) * 180 : 0;
      return `
        <div class="bar-wrapper">
          <div class="bar-value">${bar.value}</div>
          <div class="bar" style="height: ${height}px"></div>
          <div class="bar-label">${bar.label}</div>
        </div>
      `;
    }).join('') + '</div></div>';
}
```

## Helper Functions

Include these as needed:
```javascript
function formatCurrency(n) {
  return new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD', maximumFractionDigits: 0 }).format(n);
}

function formatNumber(n) {
  return new Intl.NumberFormat('en-US').format(n);
}

function formatCompact(n) {
  if (n >= 1000000) return (n/1000000).toFixed(1) + 'M';
  if (n >= 1000) return (n/1000).toFixed(1) + 'K';
  return n.toString();
}

function formatDate(dateStr) {
  return new Date(dateStr).toLocaleDateString('en-US', { month: 'short', day: 'numeric', year: 'numeric' });
}
```

## CSS Patterns

### Common Styles
```css
* { box-sizing: border-box; margin: 0; padding: 0; }
body { font-family: system-ui, -apple-system, sans-serif; padding: 16px; background: #fff; color: #1a1a1a; }
.loading { min-height: 200px; display: flex; align-items: center; justify-content: center; color: #666; }
```

### Grid Layout
```css
.grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 16px; }
```

### Cards
```css
.card { background: #f9fafb; border: 1px solid #e5e7eb; border-radius: 12px; padding: 20px; }
.card:hover { background: #f3f4f6; }
```

### Badges
```css
.badge { display: inline-block; padding: 2px 8px; border-radius: 9999px; font-size: 12px; font-weight: 500; }
.badge-green { background: #dcfce7; color: #166534; }
.badge-yellow { background: #fef9c3; color: #854d0e; }
.badge-red { background: #fee2e2; color: #dc2626; }
```

## Checklist

- [ ] Widget config added to `widgets` array
- [ ] Widget HTML added to `generateWidgetHtml()`
- [ ] Tool created/updated with `widgetId`
- [ ] Mock data provided for preview
- [ ] Preview tested at `/preview/{widget-id}`
- [ ] XSS prevention (escape user content)
