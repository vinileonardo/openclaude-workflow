# Setup Guide

## Prerequisites

- [OpenClaude](https://github.com/Gitlawb/openclaude) installed
- [OpenCode Go](https://github.com/opencode-ai/opencode-go) running as API provider
- Git

## Quick Setup (Any Project)

```bash
# 1. Start OpenClaude in your project
cd your-project
opencode

# 2. Run the setup skill inside OpenClaude
/init-workflow

# 3. Follow the prompts to confirm detected values
```

## Option A: New Project from Template

```bash
# 1. Create repo from template
gh repo create my-project --template vinileonardo/openclaude-workflow
cd my-project

# 2. Initialize git
git init
git add .
git commit -m "Initial commit: AI workflow setup"

# 3. Start OpenClaude and run setup
opencode
/init-workflow
```

## Option B: Manual Setup

Copy the template files into your project:

```bash
# Clone the workflow repo
git clone https://github.com/vinileonardo/openclaude-workflow.git /tmp/ocw
cd your-project

# Copy template files
cp -r /tmp/ccw/template/.claude .claude
cp /tmp/ccw/template/CLAUDE.md .
cp /tmp/ccw/template/AGENTS.md .

# Edit placeholders in each file
# See docs/CUSTOMIZATION.md for details
```

## Post-Setup Steps

### 1. Configure OpenClaude + OpenCode Go

You need OpenClaude connected to OpenCode Go as the API provider. Choose one option:

#### Option A: .bashrc alias (recommended, one-liner)

Add this to your `~/.bashrc` or `~/.zshrc`:

```bash
alias openclaude='OPENAI_BASE_URL=https://opencode.ai/zen/go/v1 \
  CLAUDE_CODE_USE_OPENAI=1 \
  OPENCODE_API_KEY=sk-your-key-here \
  OPENAI_MODEL=deepseek-v4-flash \
  openclaude --dangerously-skip-permissions'
```

Then reload: `source ~/.bashrc`

This single line:
- Connects OpenClaude to OpenCode Go natively
- Sets `deepseek-v4-flash` as the default session model
- Skips all permission prompts (`--dangerously-skip-permissions`)

#### Option B: settings.json + env vars

If you prefer not to use an alias, configure `~/.claude/settings.json`:

```json
{
  "model": "deepseek-v4-flash",
  "effortLevel": "high",
  "hooks": {}
}
```

And export these in your shell profile:

```bash
export CLAUDE_CODE_USE_OPENAI=1
export OPENAI_BASE_URL=https://opencode.ai/zen/go/v1
export OPENCODE_API_KEY=sk-your-key-here
export OPENAI_MODEL=deepseek-v4-flash
```

### 2. Review Generated Files

Check that `AGENTS.md` and `.claude/agents/*.md` have the correct:
- Project paths and directory structure
- Lint/test/build commands
- Docker container names
- Database and cache configuration

### 2. Configure Models

Edit `~/.claude/settings.json`:

```json
{
  "model": "deepseek-v4-flash",
  "effortLevel": "high",
  "hooks": {}
}
```

Each agent in `.claude/agents/*.md` already declares its specific model. See `docs/AGENT_TIERS.md` for details.

### 3. Initialize Memory

```bash
# Create the memory directory for your project
mkdir -p .claude/projects/$(basename $(pwd))/memory/
cp template/memory/MEMORY.md .claude/projects/$(basename $(pwd))/memory/
```

### 4. Customize for Your Stack

See `docs/CUSTOMIZATION.md` for adapting agents, models, and project-specific rules.

## Troubleshooting

| Problem | Solution |
|---|---|
| `can't find /init-workflow skill` | Copy `skills/init-workflow/` to `.claude/skills/` in your project |
| Agent commands are wrong | Edit the `.md` file in `.claude/agents/` to fix paths |
| Agent not using expected model | Check frontmatter `model` field in the agent file |
| Memory not persisting | Verify `.claude/projects/<slug>/memory/MEMORY.md` exists |
| Permission prompts too frequent | Add allow rules to `.claude/settings.json` |
