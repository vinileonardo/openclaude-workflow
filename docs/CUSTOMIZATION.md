# Customization Guide

## Adding a New Agent

1. Create `.claude/agents/<name>.md`:

```yaml
---
name: my-agent
description: What this agent does
tools: Read, Grep, Glob, Bash, Write, Edit
model: qwen3.7-plus        # deepseek-v4-pro, qwen3.7-plus, or deepseek-v4-flash
permissionMode: acceptEdits
memory: project
color: blue
---

You are a specialized agent that...
```

2. Add the agent to `AGENTS.md` taxonomy table.

3. Define its role in the orchestration flow.

## Modifying an Agent

Edit the file in `.claude/agents/<name>.md`:

- **Frontmatter**: Change model, permissions, color
- **Description**: Update what the agent does
- **Body**: Add project-specific rules, commands, patterns

## Changing Model Assignments

### Per Agent

Edit the `model` field in the agent's frontmatter:

```yaml
model: deepseek-v4-pro   # Deep thinking
model: qwen3.7-plus      # Balanced workhorse
model: deepseek-v4-flash # Fast & cheap
```

### Global (session default)

Set in `~/.claude/settings.json`:

```json
{
  "model": "deepseek-v4-flash"
}
```

## Project-Specific Rules in AGENTS.md

Add a "Guardrails" section with your project's rules:

```markdown
## Guardrails

- Database: Always use parameterized queries
- Auth: JWT tokens expire after 15 minutes
- Error format: `{ error: string, code: string }`
- Logging: Use structured JSON logs
```

## Maintaining Memory

### Save a Memory

When you learn something important during a session:

1. Create a new `.md` file in `.claude/projects/<slug>/memory/`
2. Use frontmatter:
   ```yaml
   ---
   name: short-kebab-name
   description: What this memory contains
   metadata:
     type: feedback | project | user | reference
   ---
   Content here with context...
   ```
3. Add a pointer in `MEMORY.md`

### Memory Types

| Type | When | Example |
|---|---|---|
| user | User's role, preferences, knowledge | "User is a senior backend engineer" |
| feedback | Corrections or confirmed approaches | "Always validate server-side" |
| project | Ongoing context, decisions | "Sprint 12 focuses on payment refactor" |
| reference | External resources | "Bug tracker at linear.app/project" |

## CI/CD Integration

Integrate the workflow with your pipeline:

```yaml
# .github/workflows/review.yml
name: AI Code Review
on: [pull_request]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: AI Review
        run: |
          echo "Trigger OpenClaude review here"
```

## Multi-Language Projects

For projects with multiple languages, update agents accordingly:

- Create separate backend agents per language (e.g., `backend-python-dev.md`, `backend-node-dev.md`)
- Update the orchestrator in `AGENTS.md` to select the right agent
- Use distinct commands per stack in each agent file
