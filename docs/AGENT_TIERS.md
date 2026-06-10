# Model Tier Philosophy

This project uses a **3-tier model strategy** that balances cost, speed, and quality.

## The Three Tiers

### Haiku — Fast & Cheap

| Attribute | Value |
|---|---|
| **Purpose** | Read-only, quick tasks, exploration |
| **Assigned to** | Explorer, Documentation Writer |
| **Cost** | $ |
| **Speed** | Fastest |
| **When to use** | Browsing code, simple writes, grep/glob searches |

Ideal for agents that browse, summarize, and produce straightforward output. No deep reasoning needed.

### Sonnet — Balanced Workhorse

| Attribute | Value |
|---|---|
| **Purpose** | Implementation, coding, testing |
| **Assigned to** | Backend Dev, Frontend Dev, Test Writer, DevOps |
| **Cost** | $$ |
| **Speed** | Fast |
| **When to use** | Writing code, running commands, editing files |

The default tier for agents that produce code. Balances quality and speed for most development tasks.

### Opus — Deep Thinking

| Attribute | Value |
|---|---|
| **Purpose** | Analysis, review, decisions |
| **Assigned to** | Spec Driver, Product Owner, Code Reviewer, Security Reviewer |
| **Cost** | $$$ |
| **Speed** | Slowest |
| **When to use** | Specs, issue creation, code review, security audit |

Used when quality and correctness matter more than speed. Agents in this tier catch bugs, design architecture, and make decisions.

## Why Tiered?

1. **Cost optimization**: You don't need Opus to find a file. You don't want Haiku reviewing auth logic.
2. **Speed matching**: Explorer can scan in seconds on Haiku. Security Reviewer needs deep context.
3. **Quality gates**: Critical paths (review, security) use the best model available.

## Agent to Tier Mapping

| Agent | Tier | Why |
|---|---|---|
| spec-driver | opus | Needs deep understanding to decompose requests |
| product-owner | opus | Issue quality affects entire sprint |
| explorer | haiku | Read-only, fast scanning |
| backend-dev | sonnet | Coding needs good but not maximal reasoning |
| frontend-dev | sonnet | Same as backend-dev |
| test-writer | sonnet | Follows patterns, needs moderate reasoning |
| code-reviewer | opus | Catches bugs, needs full context |
| security-reviewer | opus | Security is critical, needs thorough analysis |
| documentation-writer | haiku | Follows templates, simple writing |
| devops | sonnet | Commands and configs, moderate reasoning |

## Configuration Options

### Option 1: Native Claude Code (no proxy)

Set your model in `~/.claude/settings.json`:

```json
{
  "model": "haiku",
  "opusModel": "opus",
  "sonnetModel": "sonnet"
}
```

Agent frontmatter `model: inherit` uses the session model. Set `model: opus` or `model: haiku` in an agent's frontmatter to pin it.

### Option 2: With Proxy (oc-go-cc, openrouter, etc.)

Configure model mapping in your proxy config. The agent frontmatter `model: opus` sends a request identifying as `claude-opus-4-6`, which your proxy routes to your chosen model.

Example proxy routing:

```
claude-haiku-4-5-20251001  →  deepseek-v4-flash (or claude-3-haiku)
claude-sonnet-4-6          →  qwen3.7-plus (or claude-3.5-sonnet)
claude-opus-4-6            →  deepseek-v4-pro (or claude-opus-4)
```

### Option 3: Direct Model Names

Set `model: claude-sonnet-4-6` directly in agent frontmatter to bypass tier routing.

## Effort Level

Agents also respect an `effortLevel` setting:

| Level | Behavior |
|---|---|
| low | Concise responses, less context, faster |
| medium | Balanced (default) |
| high | Detailed responses, full context, slower |

Set in `~/.claude/settings.json`:

```json
{
  "effortLevel": "medium"
}
```

## Fallback Strategy

Configure fallbacks for reliability:

1. **Primary**: Your preferred model for each tier
2. **Fallback 1**: Alternative provider (different API)
3. **Fallback 2**: Cheaper model (rate limits)
