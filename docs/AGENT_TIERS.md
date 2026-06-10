# Model Philosophy

This project uses a **3-model strategy** that balances cost, speed, and quality, powered by **OpenCode Go** native models.

## The Three Models

### deepseek-v4-flash — Fast & Cheap

| Attribute | Value |
|---|---|
| **Purpose** | Read-only, quick tasks, exploration |
| **Assigned to** | Explorer, Documentation Writer |
| **Speed** | Fastest |
| **Cost** | Lowest |
| **When to use** | Browsing code, simple writes, grep/glob searches |

Ideal for agents that browse, summarize, and produce straightforward output. No deep reasoning needed.

### qwen3.7-plus — Balanced Workhorse

| Attribute | Value |
|---|---|
| **Purpose** | Implementation, coding, testing |
| **Assigned to** | Backend Dev, Frontend Dev, Test Writer, DevOps |
| **Speed** | Fast |
| **Cost** | Medium |
| **When to use** | Writing code, running commands, editing files |

The default model for agents that produce code. Balances quality and speed for most development tasks.

### deepseek-v4-pro — Deep Thinking

| Attribute | Value |
|---|---|
| **Purpose** | Analysis, review, decisions |
| **Assigned to** | Spec Driver, Product Owner, Code Reviewer, Security Reviewer |
| **Speed** | Slower |
| **Cost** | Higher |
| **When to use** | Specs, issue creation, code review, security audit |

Used when quality and correctness matter more than speed. Agents in this tier catch bugs, design architecture, and make decisions.

## Why Three Models?

1. **Cost optimization**: You don't need deepseek-v4-pro to find a file. You don't want deepseek-v4-flash reviewing auth logic.
2. **Speed matching**: Explorer can scan in seconds on deepseek-v4-flash. Security Reviewer needs deep context.
3. **Quality gates**: Critical paths (review, security) use the best model available.

## Agent to Model Mapping

| Agent | Model | Why |
|---|---|---|
| spec-driver | deepseek-v4-pro | Needs deep understanding to decompose requests |
| product-owner | deepseek-v4-pro | Issue quality affects entire sprint |
| explorer | deepseek-v4-flash | Read-only, fast scanning |
| backend-dev | qwen3.7-plus | Coding needs good but not maximal reasoning |
| frontend-dev | qwen3.7-plus | Same as backend-dev |
| test-writer | qwen3.7-plus | Follows patterns, needs moderate reasoning |
| code-reviewer | deepseek-v4-pro | Catches bugs, needs full context |
| security-reviewer | deepseek-v4-pro | Security is critical, needs thorough analysis |
| documentation-writer | deepseek-v4-flash | Follows templates, simple writing |
| devops | qwen3.7-plus | Commands and configs, moderate reasoning |

## Configuration

### Native OpenClaude (default)

Configure your model in `~/.claude/settings.json`:

```json
{
  "model": "deepseek-v4-flash",
  "permissionConfig": {
    "autoAllow": true
  }
}
```

Each agent's frontmatter declares its specific model. For example:

```yaml
---
name: code-reviewer
model: deepseek-v4-pro
---
```

### Effort Level

Agents also respect an `effortLevel` setting:

| Level | Behavior |
|---|---|
| low | Concise responses, less context, faster |
| medium | Balanced |
| high | Detailed responses, full context, slower |

Set in `~/.claude/settings.json`:

```json
{
  "effortLevel": "high"
}
```

### Fallback Strategy

You can configure fallbacks in your provider settings for reliability:

1. **Primary**: Your preferred model for each tier
2. **Fallback 1**: Alternative provider (different API)
3. **Fallback 2**: Cheaper model (rate limits)

See `archive/oc-go-cc-config.example.json` for a proxy-based fallback example (kept for historical reference).
