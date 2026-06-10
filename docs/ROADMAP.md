# Roadmap

> Vision for future evolution of OpenClaude Workflow.
> Ideas are not commitments — priorities shift based on feedback.

## Short Term

### Agent Communication Protocol — Refinements

- [ ] Validate artifact templates against real project usage
- [ ] Add ADR template to the protocol (Architecture Decision Records)
- [ ] Auto-generate artifact files from agent output (instead of manual creation)

### GitHub Integration

- [ ] Auto-create issue templates via `gh` during `--github` mode
- [ ] Create labels automatically as part of the workflow
- [ ] Add `.github/labeler.yml` for PR auto-labeling

## Medium Term

### Alternative Git Providers

- [ ] GitLab integration guide (issue hierarchy, CI/CD, merge requests)
- [ ] Bitbucket integration guide (pipelines, PRs)

### Project Management Integration

- [ ] Linear integration (issue creation, teams, cycles)
- [ ] Jira integration (epics, stories, sprints)
- [ ] Notion integration (docs, wikis)

### Template Expansion

- [ ] ADR templates in `template/docs/adr/`
- [ ] PR template (`.github/PULL_REQUEST_TEMPLATE.md`)
- [ ] Code of conduct and contributing guides
- [ ] Docker Compose templates for common stacks

### Skill Enhancements

- [ ] `/init-workflow --migrate` — migrate from other workflow systems (Cursor, Copilot)
- [ ] `/init-workflow --export` — export workspace config for team sharing
- [ ] `/init-workflow --upgrade` — upgrade template version in place (read CHANGELOG, apply diffs)

## Long Term

### Agent Ecosystem

- [ ] Plugin system for custom agents (bring your own agent definition)
- [ ] Agent registry — share agent definitions between projects
- [ ] Review analytics — track code review metrics over time

### Multi-Team Support

- [ ] Team-wide agent templates shared via git
- [ ] Per-team model configurations
- [ ] Centralized agent registry across repositories

### AI-Native Workflow

- [ ] Self-healing workflow — agents detect and fix configuration drift automatically
- [ ] Automated memory pruning — identify and archive stale memories
- [ ] Workflow analytics — which agents are used most, where the pipeline blocks

---

## How to Contribute

See `CONTRIBUTING.md` or open an issue/PR at:
https://github.com/vinileonardo/openclaude-workflow
