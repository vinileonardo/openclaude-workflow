# Changelog

All notable changes to the OpenClaude Workflow template will be documented in this file.

## [1.0.0] — 2026-06-10

### Added

- **Greenfield mode** (`/init-workflow`): full multi-agent workflow setup for new projects, with stack detection, agent definitions, CLAUDE.md, AGENTS.md, memory, and settings
- **Adoption mode** (`/init-workflow --adopt`): inventory, memory bootstrapping, diff plan, and merge for existing projects with custom setups
- **Diagnostic mode** (`/init-workflow --health`): 6 checks (file integrity, placeholders, agents, settings, memory, commands)
- **Cleanup mode** (`/init-workflow --prune`): remove orphan agents, stale temp files, unindexed memories
- **Update mode** (`/init-workflow --update`): regenerate missing files without overwriting customizations
- **10 specialized agents**: spec-driver, product-owner, explorer, backend-dev, frontend-dev, test-writer, code-reviewer, security-reviewer, documentation-writer, devops
- **3-model tier strategy**: deepseek-v4-flash (fast), qwen3.7-plus (workhorse), deepseek-v4-pro (deep thinking)
- **Agent Communication Protocol**: structured artifact templates for each agent defining input/output contracts
- **Runbooks**: adding-a-feature, fixing-a-bug, security-review, codebase-audit, first-task
- **Memory system**: typed memories (user, feedback, project, reference) with persistent cross-session context
- **AI_ENTRYPOINT.md**: dedicated entrypoint for AI agents directed to this repository
- **GitHub Integration Guide**: comprehensive docs for token, projects, milestones, epics, issue templates, Actions
- **VERSION file**: sematic versioning for the template
