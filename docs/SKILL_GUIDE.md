# Skill Guide: /init-workflow

> Complete reference for the `/init-workflow` skill — the multi-agent AI workflow setup for OpenClaude.

## Overview

The `/init-workflow` skill sets up a complete multi-agent AI development environment in any project. It detects your stack, generates adapted agent definitions, and optionally bootstraps memory from existing code.

Two modes:

| Mode | Command | When to Use |
|---|---|---|
| **Greenfield** | `/init-workflow` | New projects, or projects without any existing setup |
| **Adoption** | `/init-workflow --adopt` | Existing projects with code, conventions, and possibly existing workflow |
| **Diagnostic** | `/init-workflow --health` | Any project — check workflow consistency |
| **Cleanup** | `/init-workflow --prune` | Any project — remove orphans, stale temps, unindexed memories |

---

## Commands

```bash
/init-workflow                        # Full setup (greenfield)
/init-workflow --check                # Discover only, no file generation
/init-workflow --update               # Regenerate from existing template
/init-workflow --adopt                # Adopt existing project
/init-workflow --adopt --dry-run      # Preview adoption changes without writing
/init-workflow --health               # Diagnostic: check workflow consistency
/init-workflow --prune                # Cleanup: remove orphans, stale temps
/init-workflow --prune --dry-run      # Preview what would be pruned
/init-workflow --prune --force        # Prune without confirmation prompt
```

---

## Greenfield Mode (`/init-workflow`)

Best for: **starting a new project**, or adopting the workflow where no previous setup exists.

### Phases

1. **Discovery** — Read-only scan of your project (stack, commands, structure, architecture)
2. **Generation** — Creates adapted files in `.claude/workflow-setup/`
3. **Confirmation** — Shows detected stack and asks for confirmation
4. **Guidance** — Explains how to use the workflow

### What's Generated

| File | Description |
|---|---|
| `CLAUDE.md` | Project summary with stack, commands, architecture |
| `AGENTS.md` | Full orchestration workflow with 10 agents |
| `.claude/agents/*.md` | 10 individual agent definitions |
| `.claude/settings.json` | OpenClaude configuration |
| `memory/MEMORY.md` | Memory index template |
| `.github/ISSUE_TEMPLATE/*.md` | Issue templates (if GitHub opt-in) |
| `.github/workflows/*.yml` | CI/CD workflows (if GitHub opt-in) |

---

## GitHub Integration (Optional)

During both greenfield and adoption setup, you're asked if you want GitHub integration.

### What It Does

- Generates `.github/ISSUE_TEMPLATE/` (epic, story, task, bug)
- Generates `.github/workflows/` (CI, deploy, review automation)
- Creates labels: `epic`, `story`, `task`, `spike`, `bug`
- Offers branch protection configuration

### When to Opt In

- You use GitHub Issues and Projects for task management
- You want CI/CD via GitHub Actions
- You want standardized issue templates for the agent workflow

### When to Opt Out

- You use a different Git provider (GitLab, Bitbucket)
- You have your own issue/CI setup already
- You just want the agent workflow without GitHub extras

### More Details

See `docs/GITHUB_INTEGRATION.md` for the complete guide.

---

## Update Mode (`/init-workflow --update`)

Best for: **projects already using this workflow** that need to pick up new template files without losing customizations.

### What It Does

- Runs the same discovery as greenfield mode
- Creates missing files only (CLAUDE.md, AGENTS.md, agents, settings, memories)
- **Never overwrites** existing files — only adds what's missing

### When to Use

- After pulling a newer version of the `openclaude-workflow` template
- To add newly introduced agents or docs to an existing setup
- After renaming the project or changing the stack

### Comparison

| Flag | When | What Gets Written |
|---|---|---|
| *(none)* | First setup | Everything from template |
| `--update` | Already set up | Only missing files |
| `--adopt` | Existing project | Merge + preserve + bootstrap |

---

## Adoption Mode (`/init-workflow --adopt`)

Best for: **existing projects** with code written, conventions established, and possibly a partial or different workflow setup already in place.

### Phases

#### A1 — Inventory
Scans everything that already exists:
- Existing `CLAUDE.md`, `AGENTS.md`, agent files, settings, memories
- Adjacent configs (Cursor rules, Copilot instructions, VS Code settings)
- Git history for commit style, branch naming, and hotspots

**No files modified.**

#### A2 — Memory Bootstrapping
Extracts knowledge from existing code automatically:
- Coding conventions (naming, imports, error handling, tests)
- Architecture patterns (Repository, Service Layer, MVC, etc.)
- External integrations and services
- CI/CD and deployment structure

Conventions become structured `project` and `reference` memories.

#### A3 — Diff Plan
Shows exactly what will happen before writing anything:

| Label | Meaning |
|---|---|
| 📄 CREATE | New files from template |
| 📝 OVERWRITE | Existing files that will be merged |
| ✅ PRESERVE | Existing files left untouched |
| 🔄 MERGE CONFLICT | Items needing your decision |
| 🧠 BOOTSTRAP | New memories from code analysis |

Use `--dry-run` to see this without writing anything.

#### A4 — Merge & Write
Intelligent merge rules:

| File | Merge Strategy |
|---|---|
| `CLAUDE.md` | Merge sections — your rules kept, template sections added |
| `AGENTS.md` | Preserve custom agents, add missing template agents |
| Agent files | Custom versions kept, missing ones created from template |
| `settings.json` | Never overwritten if it exists |
| Memories | Bootstrapped memories appended, existing ones preserved |
| Adjacent configs | Rules imported into workflow, originals untouched |

#### A5 — Adoption Report
Summary of what changed, what was preserved, and suggested next steps.

---

## Diagnostic Mode (`/init-workflow --health`)

Best for: **any project** — checks if the workflow setup is consistent and healthy. Read-only, never modifies files.

### Checks Performed

| Check | What It Verifies |
|---|---|
| **File Integrity** | `CLAUDE.md`, `AGENTS.md`, `.claude/`, `settings.json`, `MEMORY.md` exist |
| **Placeholder Audit** | Scans all workflow files for unfilled `{{...}}` placeholders |
| **Agent Consistency** | All agents in `AGENTS.md` have `.md` files; no orphans; frontmatter valid |
| **Settings Validation** | `settings.json` is valid JSON, model is known, hooks syntax valid |
| **Memory Health** | All files listed in `MEMORY.md` exist; no orphan memory files |
| **Command Dry-Run** | Lint/test/build paths from config are syntactically valid |

### Severity

| Icon | Meaning |
|---|---|
| ✅ PASS | No action needed |
| ⚠️ WARNING | Non-blocking (unfilled placeholder, orphan file) |
| ❌ FAIL | Blocking (missing critical file, invalid JSON) |

### When to Run

- After greenfield setup or adoption to verify correctness
- Periodically to catch drift as the project evolves
- Before major changes to ensure the workflow is healthy

---

## Cleanup Mode (`/init-workflow --prune`)

Best for: **any project** — removes orphaned and stale files left behind as the project evolves.

### What Gets Pruned

| Item | Deletes |
|---|---|
| **Orphan Agents** | `.claude/agents/*.md` not referenced in `AGENTS.md` |
| **Stale Temp Files** | `.claude/workflow-setup/` directory |
| **Orphan Memories** | Files in `memory/` not listed in `MEMORY.md` |
| **Empty Dirs** | Empty directories left after pruning |

### Behavior

| Flag | Behavior |
|---|---|
| `--prune` | Preview + confirm before deleting |
| `--prune --dry-run` | Preview only, no deletion |
| `--prune --force` | Skip confirmation, prune immediately |

---

## Flags Reference

| Flag | Mode | Effect |
|---|---|---|
| *(no flag)* | Greenfield | Full discovery + generation + confirmation |
| `--check` | Both | Discover only — no files written |
| `--update` | Update | Regenerate missing files only (preserves existing) |
| `--adopt` | Adoption | Full inventory + bootstrap + diff + merge |
| `--adopt --dry-run` | Adoption | Preview only — no files written |
| `--adopt --check` | Adoption | Skip to inventory only |
| `--health` | Diagnostic | Run all checks, read-only, no files written |
| `--prune` | Cleanup | Remove orphans + stale temps (preview first) |
| `--prune --dry-run` | Cleanup | Preview only, no deletion |
| `--prune --force` | Cleanup | Prune without confirmation |

---

## Template Source

The skill reads from the `openclaude-workflow` template at:
- `template/CLAUDE.md`
- `template/AGENTS.md`
- `template/.claude/agents/*.md`
- `template/.claude/settings.json`
- `template/memory/MEMORY.md`

Detection patterns reference:
- `references/discovery-patterns.md` — Stack/command detection rules
- `references/stack-patterns.md` — Stack → placeholder mappings

---

## Model Tiers

The workflow uses a 3-model strategy via OpenCode Go:

| Model | Speed | Best For | Agents |
|---|---|---|---|
| `deepseek-v4-flash` | Fastest | Read-only, scanning, simple output | Explorer, Doc Writer |
| `qwen3.7-plus` | Fast | Implementation, commands, testing | Backend/Frontend/Test/DevOps |
| `deepseek-v4-pro` | Slower | Deep analysis, reviews, decisions | Spec/PO/Code/Security Review |

---

## Customization

After setup, you can customize:

### Add a New Agent
Create `.claude/agents/<name>.md` with frontmatter and instructions. Add it to `AGENTS.md`.

### Change Model Assignments
Edit the `model` field in any agent's frontmatter.

### Project Guardrails
Add a "Guardrails" section in `AGENTS.md` for project-specific rules.

### Maintain Memory
Save important context as typed memories (`user`, `feedback`, `project`, `reference`) in `.claude/projects/<slug>/memory/`.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| Skill not found (`can't find /init-workflow`) | Copy `skills/init-workflow/` to `.claude/skills/` in your project |
| Detected stack is wrong | Edit the generated `AGENTS.md` placeholders manually |
| Agent commands are incorrect | Edit the `.md` file in `.claude/agents/` for the specific agent |
| Agent using wrong model | Check `model` field in the agent's frontmatter |
| Memory not persisting | Verify `.claude/projects/<slug>/memory/MEMORY.md` exists |
| `--adopt` overwrote my settings | Re-run with `--adopt --dry-run` first to preview |
| Too many permission prompts | Add allow rules to `.claude/settings.json` |
| Workflow feels broken after changes | Run `/init-workflow --health` to diagnose |
