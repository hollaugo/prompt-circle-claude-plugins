---
name: chatgpt-app:golden-prompts
description: Generate test prompts to validate that ChatGPT will correctly invoke your app's tools.
---

# Generate Golden Prompts

You are helping the user generate golden prompts to test their ChatGPT App.

## What Are Golden Prompts?

Golden prompts are a testing dataset that validates ChatGPT will correctly invoke your app's tools.
Based on the [OpenAI Apps SDK metadata optimization guide](https://developers.openai.com/apps-sdk/guides/optimize-metadata).

### Three Categories

1. **Direct Prompts** - Users explicitly name your product/action
   - "Use [App Name] to..."
   - "Create a [specific action]..."

2. **Indirect Prompts** - Users describe desired outcomes without naming the tool
   - "I need to..."
   - "Help me..."
   - Natural language describing the problem

3. **Negative Prompts** - Cases requiring alternative tools or no tool
   - Questions about the domain (informational, not actionable)
   - Requests outside your app's scope
   - Edge cases that look similar but shouldn't trigger

## Why Golden Prompts Matter

- **Precision**: Measures how often tool invocations are correct
- **Recall**: Measures how often the tool is invoked when it should be
- **Iteration**: Test one metadata field at a time to attribute improvements
- **Post-launch monitoring**: Track against this dataset after deployment

## Workflow

1. **Analyze Tools**
   Read the app's tools from state or server code.
   Pay attention to:
   - Tool names (should use domain-action pairs like `calendar.create_event`)
   - Tool descriptions (should start with "Use this when..." and state limitations)
   - Input parameters (clear descriptions and examples)

2. **Generate Direct Prompts (5+ per tool)**
   Create prompts that explicitly reference the action:
   ```
   "Create a new task called Buy groceries"
   "Use Task Manager to add Pick up dry cleaning"
   "Make a new task: Call mom"
   ```

3. **Generate Indirect Prompts (5+ per tool)**
   Create prompts describing the goal:
   ```
   "I need to remember to buy groceries"
   "Don't let me forget to pick up dry cleaning"
   "Remind me to call mom this week"
   ```

4. **Generate Negative Prompts (3+ per category)**
   Create prompts that shouldn't trigger the tool:
   ```
   "What is a task?"
   "How do I prioritize my tasks?"
   "Tell me about task management best practices"
   ```

5. **Generate Edge Case Prompts (2+ per tool)**
   Create prompts that test boundary conditions:
   ```
   "Create a task" (missing required details)
   "Delete all my tasks" (dangerous operation)
   "Create 100 tasks" (bulk operation)
   ```

6. **Save Prompts**
   Write to `.chatgpt-app/golden-prompts.json`

## Output Format

```json
{
  "generatedAt": "2024-01-15T12:00:00Z",
  "appName": "my-app",
  "tools": {
    "create_task": {
      "direct": ["Create a new task...", ...],
      "indirect": ["I need to remember...", ...],
      "negative": ["What is a task?", ...],
      "edgeCases": ["Create a task", ...]
    }
  },
  "metadata": {
    "totalPrompts": 45,
    "promptsPerTool": {
      "create_task": { "direct": 5, "indirect": 5, "negative": 3, "edgeCases": 2 }
    }
  }
}
```

## Tool Description Best Practices

When reviewing tools, check that descriptions follow these patterns:

**Good Description:**
```
"Use this when the user wants to create a new task with a title and optional due date.
Do not use for reminders or calendar events."
```

**Bad Description:**
```
"Creates a task"
```

## Testing Workflow

1. Generate prompts: `/chatgpt-app:golden-prompts`
2. Review and edit: `.chatgpt-app/golden-prompts.json`
3. Run tests: `/chatgpt-app:test`
4. Iterate on metadata based on results
5. Re-test until precision/recall targets met

## Display Summary

```
## Golden Prompts Generated

### create_task
- Direct: 5 prompts ✓
- Indirect: 5 prompts ✓
- Negative: 3 prompts ✓
- Edge Cases: 2 prompts ✓

### list_tasks
- Direct: 5 prompts ✓
- Indirect: 5 prompts ✓
- Negative: 3 prompts ✓
- Edge Cases: 2 prompts ✓

---
Total: 30 prompts across 2 tools

Run `/chatgpt-app:test` to validate these prompts.
```
