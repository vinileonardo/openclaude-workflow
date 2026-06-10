# OpenClaude Workflow

A professional multi-agent AI development workflow for OpenClaude. Drop-in setup for any project.

## Quick Start

```bash
# Existing project: start OpenClaude, run the setup
cd your-project
opencode
/init-workflow
```

The skill analyzes your stack, commands, and architecture, then generates a complete agent ecosystem adapted to your project.

## Features

- **10 Specialized Agents** — From spec to deploy, each with a clear role and model
- **Native OpenCode Go Models** — deepseek-v4-pro, qwen3.7-plus, deepseek-v4-flash — mapped to agents by task complexity
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

| Agent | Model | Purpose |
|---|---|---|
| spec-driver | deepseek-v4-pro | SDD specs from vague requests |
| product-owner | deepseek-v4-pro | Issues, backlog, priorities |
| explorer | deepseek-v4-flash | Codebase mapping, research |
| backend-dev | qwen3.7-plus | API, logic, database |
| frontend-dev | qwen3.7-plus | UI, components, state |
| test-writer | qwen3.7-plus | Unit/integration tests |
| code-reviewer | deepseek-v4-pro | Quality, bugs, standards |
| security-reviewer | deepseek-v4-pro | OWASP, audit, compliance |
| documentation-writer | deepseek-v4-flash | Docs, changelog, ADRs |
| devops | qwen3.7-plus | Docker, CI/CD, deploy |

## Model Guide

| Model | Provider | Speed | Best For | Agents |
|---|---|---|---|---|
| deepseek-v4-flash | DeepSeek | Fastest | Read-only, scanning, simple output | Explorer, Doc Writer |
| qwen3.7-plus | Qwen | Fast | Implementation, commands, testing | Backend/Frontend/Test/DevOps |
| deepseek-v4-pro | DeepSeek | Slower | Deep analysis, reviews, decisions | Spec/PO/Code/Security Review |

## Project Structure

```
openclaude-workflow/
├── template/                          # → Copy into target project
│   ├── CLAUDE.md                      # Pointer with placeholders
│   ├── AGENTS.md                      # Orchestration workflow
│   ├── .claude/
│   │   ├── settings.json              # OpenClaude config
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
│   └── AGENT_TIERS.md                 # Model philosophy
└── archive/
    └── oc-go-cc-config.example.json   # Archived proxy reference
```

## License

MIT
