# Claude Code Workflow

A professional multi-agent AI development workflow for Claude Code. Drop-in setup for any project.

## Quick Start

```bash
# Existing project: start Claude, run the setup
cd your-project
claude
/init-workflow
```

The skill analyzes your stack, commands, and architecture, then generates a complete agent ecosystem adapted to your project.

## Features

- **10 Specialized Agents** — From spec to deploy, each with a clear role and model tier
- **Model Tier Matrix** — Haiku (fast/cheap), Sonnet (balanced), Opus (deep thinking) — mapped to agents by task complexity
- **Full Orchestration Pipeline** — spec-driver → product-owner → explorer → devs → test-writer → code-reviewer + doc-writer → security-reviewer → devops
- **Auto-Adaptation** — `/init-workflow` reads your project and generates files with correct paths and commands
- **Structured Memory System** — Persistent cross-session context with typed memories (user, feedback, project, reference)
- **Reviewer Decision Trees** — Clear rules for blocking vs. non-blocking feedback
- **Quality Gates** — Code review before security review before merge

## Agent Workflow

```
spec-driver → product-owner → explorer → devs → test-writer → code-reviewer
                                                                  + doc-writer (parallel)
                                                                       ↓
                                                              security-reviewer
                                                                       ↓
                                                                    devops
```

### Agent Roles

| Agent | Tier | Purpose |
|---|---|---|
| spec-driver | opus | SDD specs from vague requests |
| product-owner | opus | Issues, backlog, priorities |
| explorer | haiku | Codebase mapping, research |
| backend-dev | sonnet | API, logic, database |
| frontend-dev | sonnet | UI, components, state |
| test-writer | sonnet | Unit/integration tests |
| code-reviewer | opus | Quality, bugs, standards |
| security-reviewer | opus | OWASP, audit, compliance |
| documentation-writer | haiku | Docs, changelog, ADRs |
| devops | sonnet | Docker, CI/CD, deploy |

## Model Tier Guide

| Tier | Cost | Speed | Best For | Agents |
|---|---|---|---|---|
| Haiku | $ | Fastest | Read-only, scanning, simple output | Explorer, Doc Writer |
| Sonnet | $$ | Fast | Implementation, commands, testing | Backend/Frontend/Test/DevOps |
| Opus | $$$ | Slowest | Deep analysis, reviews, decisions | Spec/PO/Code/Security Review |

## Project Structure

```
claude-code-workflow/
├── template/                          # → Copy into target project
│   ├── CLAUDE.md                      # Pointer with placeholders
│   ├── AGENTS.md                      # Orchestration workflow
│   ├── .claude/
│   │   ├── settings.json              # Basic config
│   │   └── agents/                    # 10 agent definitions
│   └── memory/
│       ├── MEMORY.md                  # Memory index template
│       └── _examples/                 # Example memories
├── skills/
│   └── init-workflow/                 # /init-workflow skill
│       ├── SKILL.md                   # Skill instructions
│       └── references/                # Detection patterns
├── docs/
│   ├── SETUP.md                       # Setup guide
│   ├── CUSTOMIZATION.md               # Adaptation guide
│   └── AGENT_TIERS.md                 # Model tier philosophy
└── proxy/
    └── oc-go-cc-config.example.json   # Example proxy config
```

## License

MIT
