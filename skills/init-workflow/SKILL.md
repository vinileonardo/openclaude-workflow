---
name: init-workflow
description: Analyze your project and set up the full AI workflow ecosystem — agents, CLAUDE.md, AGENTS.md, memory structure, all adapted to your stack.
user-invocable: true
---

# /init-workflow — AI Workflow Setup

Analyzes your project and generates a complete multi-agent AI development workflow adapted to your stack, commands, and architecture.

## How It Works

The skill runs in 4 phases:

### Phase 1: Discovery (Read-Only)

I explore your project to detect:

1. **Stack** — what languages, frameworks, and tools you use
2. **Commands** — lint, test, build, typecheck, dev scripts
3. **Structure** — directory layout, where code lives
4. **Architecture** — patterns, state management, auth, database
5. **Existing docs** — README, any existing AGENTS.md or CLAUDE.md

> **No files are modified during this phase.** I only read.

### Phase 2: Generation

I generate adapted files into `.claude/workflow-setup/`:

- `CLAUDE.md` — project-specific pointer
- `AGENTS.md` — full orchestration workflow
- `.claude/agents/*.md` — all 10 agent definitions
- `.claude/settings.json` — basic Claude Code config
- `memory/MEMORY.md` — memory index template

All files are adapted to your detected stack with correct paths and commands.

### Phase 3: Confirmation

I show you:
- What stack was detected
- What commands were found
- What files would be created

You can confirm or ask for adjustments before anything is written to your project root.

### Phase 4: Guidance

After setup, I explain:
- How to use the agent workflow
- How the model tier system works
- Where to customize further

## What Gets Generated

### CLAUDE.md
Pointer file with project summary, stack info, and links to AGENTS.md and agent configs.

### AGENTS.md
The orchestration workflow containing:
- 10-agent taxonomy with model tiers
- Orchestration flow (spec-driver → PO → explorer → devs → test → review → security → deploy)
- Parallelization rules and dependencies
- Reviewer decision trees
- Sprint completion checklist

### Agent Definitions (`.claude/agents/*.md`)

| Agent | Tier | Purpose |
|---|---|---|
| **spec-driver** | opus | Spec Driven Development — refines requests into scenarios |
| **product-owner** | opus | Creates issues, manages backlog, writes user stories |
| **explorer** | haiku | Codebase research, architecture mapping, dependency analysis |
| **backend-dev** | sonnet | API implementation, business logic, database operations |
| **frontend-dev** | sonnet | UI components, state management, API integration |
| **test-writer** | sonnet | Unit/integration tests, coverage |
| **code-reviewer** | opus | Quality review, bug detection, standards compliance |
| **security-reviewer** | opus | Security audit, OWASP Top 10, compliance |
| **documentation-writer** | haiku | Docs sync, CHANGELOG, ADRs |
| **devops** | sonnet | Docker, CI/CD, migrations, deploy |

### Settings
Basic `.claude/settings.json` with default effort level.

### Memory
`memory/MEMORY.md` template index with prepared structure for:
- User memories (role/goals)
- Feedback memories (corrections/confirmations)
- Project memories (context/decisions)
- Reference memories (external pointers)

## Usage

```bash
# Full setup — discover, generate, confirm
/init-workflow

# Quick check — only discover, don't generate
/init-workflow --check

# Regenerate from existing template
/init-workflow --update
```

## Template Source

This skill reads from the `claude-code-workflow` template repository. Files are adapted using the detected project values before being written.

## After Setup

1. Review the generated files for accuracy
2. Customize agent descriptions for your domain
3. Configure model tiers in your Claude Code settings or proxy config
4. Start using the workflow for new features
