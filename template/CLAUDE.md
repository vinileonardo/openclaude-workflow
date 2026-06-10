# {{PROJECT_NAME}} — OpenClaude Context

Condensed reference. For full workflow orchestration, see `AGENTS.md`.

## Quick Reference

- **Stack**: {{STACK_SUMMARY}}
- **Commands**: See "Critical Commands" in `AGENTS.md`
- **Architecture**: {{ARCHITECTURE_REFERENCE}}

## Agent Workflow

This project uses a structured multi-agent workflow. See `AGENTS.md` for the full orchestration pipeline:

- **Spec Driver** → **Product Owner** → **Explorer** → **Devs** → **Test Writer** → **Code Reviewer** + **Doc Writer** → **Security Reviewer** → **DevOps**

## Important Rules

- **INSERT operations**: {{INSERT_RULE}}
- **Resource ownership**: {{OWNERSHIP_RULE}} (403, never 404)
- **Environment**: Use `.env` for secrets, never hardcode
- **Memory**: Project memory lives in `.claude/projects/{{PROJECT_SLUG}}/memory/`

## Key Files

- `AGENTS.md` — Full workflow orchestration and agent definitions
- `.claude/agents/` — Individual agent configurations
- `.claude/settings.json` — OpenClaude settings
- `docs/` — Project documentation
