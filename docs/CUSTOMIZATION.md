# Customization Guide

## Adding a New Agent

1. Create `.claude/agents/<name>.md`:

```yaml
---
name: my-agent
description: What this agent does
tools: Read, Grep, Glob, Bash, Write, Edit
model: sonnet        # inherit, haiku, sonnet, or opus
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

- **Frontmatter**: Change model tier, permissions, color
- **Description**: Update what the agent does
- **Body**: Add project-specific rules, commands, patterns

## Changing Model Tier Assignments

### Per Agent

Edit the `model` field in the agent's frontmatter:

```yaml
model: opus    # Pin to opus tier
model: inherit # Use session default
```

### Global (via proxy)

Map Claude Code model names to your models:

| Claude Model | Your Model | Tier |
|---|---|---|
| `claude-haiku-4-5-20251001` | your-fast-model | Haiku |
| `claude-sonnet-4-6` | your-balanced-model | Sonnet |
| `claude-opus-4-6` | your-powerful-model | Opus |

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
          echo "Trigger Claude Code review here"
          # Claude Code + agents review the PR
```

## Multi-Language Projects

For projects with multiple languages, update agents accordingly:

- Create separate backend agents per language (e.g., `backend-python-dev.md`, `backend-node-dev.md`)
- Update the orchestrator in `AGENTS.md` to select the right agent
- Use distinct commands per stack in each agent file
