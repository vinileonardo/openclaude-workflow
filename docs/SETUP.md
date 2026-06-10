# Setup Guide

## Prerequisites

- [Claude Code](https://claude.ai/code) installed
- Git

## Option A: New Project from Template

```bash
# 1. Create repo from template
gh repo create my-project --template vinileonardo/claude-code-workflow
cd my-project

# 2. Initialize git
git init
git add .
git commit -m "Initial commit: AI workflow setup"

# 3. Start Claude Code and run setup
claude
/init-workflow
```

## Option B: Existing Project

```bash
# 1. In your project root, start Claude Code
cd your-project
claude

# 2. Run the setup skill
/init-workflow

# 3. Follow the prompts to confirm detected values
```

## Option C: Manual Setup

Copy the template files into your project:

```bash
# Clone the workflow repo
git clone https://github.com/vinileonardo/claude-code-workflow.git /tmp/ccw
cd your-project

# Copy template files
cp -r /tmp/ccw/template/.claude .claude
cp /tmp/ccw/template/CLAUDE.md .
cp /tmp/ccw/template/AGENTS.md .

# Edit placeholders in each file
# See docs/CUSTOMIZATION.md for details
```

## Post-Setup Steps

### 1. Review Generated Files

Check that `AGENTS.md` and `.claude/agents/*.md` have the correct:
- Project paths and directory structure
- Lint/test/build commands
- Docker container names
- Database and cache configuration

### 2. Configure Model Tiers

Choose your approach:

- **Simple**: Set `model: inherit` in agents and configure `~/.claude/settings.json`
- **Advanced**: Use a proxy (like oc-go-cc) for custom model routing

See `docs/AGENT_TIERS.md` for details.

### 3. Initialize Memory

```bash
# Create the memory directory for your project
mkdir -p .claude/projects/$(basename $(pwd))/memory/
cp template/memory/MEMORY.md .claude/projects/$(basename $(pwd))/memory/
```

### 4. Run a Test

Try a simple feature to verify the workflow:

```bash
claude
# "Review this project structure and suggest improvements"
# → Explorer agent activates
```

## Troubleshooting

| Problem | Solution |
|---|---|
| `can't find /init-workflow skill` | Copy `skills/init-workflow/` to `.claude/skills/` in your project |
| Agent commands are wrong | Edit the `.md` file in `.claude/agents/` to fix paths |
| Agent not using expected model | Check frontmatter `model` field or proxy config |
| Memory not persisting | Verify `.claude/projects/<slug>/memory/MEMORY.md` exists |
| Permission prompts too frequent | Add allow rules to `.claude/settings.json` |
