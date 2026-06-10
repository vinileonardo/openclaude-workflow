---
name: init-workflow
description: Analyze your project and set up the full AI workflow ecosystem — agents, CLAUDE.md, AGENTS.md, memory structure, all adapted to your stack. Supports both greenfield and existing projects.
user-invocable: true
---

# /init-workflow — AI Workflow Setup (OpenClaude)

Analyzes your project and generates a complete multi-agent AI development workflow adapted to your stack, commands, and architecture.

Supports two modes:
- **Default (greenfield)**: For new projects or projects without existing setup
- **`--adopt`**: For existing projects — inventory, merge, and bootstrap memory from code

---

## Usage

```bash
/init-workflow              # Full setup for greenfield projects
/init-workflow --check      # Only discover, don't generate
/init-workflow --update     # Regenerate from existing template

/init-workflow --adopt      # Adopt existing project (merge, preserve, bootstrap)
/init-workflow --adopt --dry-run  # Preview changes without writing

/init-workflow --health     # Diagnostic: check workflow consistency
/init-workflow --prune      # Clean up: remove orphan agents, stale temps, unindexed memories
/init-workflow --prune --dry-run  # Preview what would be pruned before deleting
```

---

## Mode: Default (Greenfield)

For new projects or projects that don't have any workflow setup yet. Four phases:

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
- `.claude/settings.json` — basic OpenClaude config
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

---

## Mode: `--update` (Regenerate)

Best for: **projects already using this workflow** that need to regenerate files from the latest template without losing customizations.

### When to Use

- After updating the `openclaude-workflow` template (new agents, improved docs)
- When you want to add missing template files to an existing setup
- After renaming the project or changing the stack summary

### What It Does

`--update` re-reads the current project (same discovery as greenfield) and regenerates **only missing or template-default files**:

| File | Behavior |
|---|---|
| `CLAUDE.md` | Only if missing |
| `AGENTS.md` | Only if missing |
| `.claude/agents/*.md` | **Never overwrites** — only adds agents that don't exist yet |
| `.claude/settings.json` | Only if missing |
| `memory/MEMORY.md` | Only if missing |
| `template/docs/agent-protocol.md` | Only if missing |
| `template/docs/runbooks/*.md` | Only if missing |

### What It Does NOT Do

- Does NOT overwrite customized agents, rules, or commands
- Does NOT modify existing memories
- Does NOT run merge or diff (use `--adopt` for that)

### Comparison

| Flag | When | What Gets Written |
|---|---|---|
| *(none)* | First setup | Everything from template |
| `--update` | Already set up, template changed | Only missing files |
| `--adopt` | Existing project with custom setup | Merge + preserve + bootstrap |

---

For projects that already have code, conventions, and possibly an existing workflow setup. This mode preserves what you have, merges what's missing, and bootstraps memory from your existing codebase.

### Phase A1: Inventory

I scan your project to build a complete picture of what already exists:

**Existing Workflow Files:**
- `CLAUDE.md` — read existing rules, commands, preferences
- `AGENTS.md` — map existing agents and customizations
- `.claude/agents/*.md` — read frontmatter, models, permissions
- `.claude/settings.json` — detect hooks, model, auto-allow rules
- `memory/MEMORY.md` + memory files — preserve all content

**Adjacent Config:**
- `.cursor/rules/` or `.cursorrules` — existing Cursor rules to incorporate
- `.github/copilot-instructions.md` — existing Copilot instructions
- `.vscode/settings.json` — editor settings relevant to workflow

**Git Conventions (from history):**
- `git log --oneline -50` — detect commit message style (conventional, semantic, etc.)
- `git branch -l` — detect branch naming patterns (feature/, fix/, hotfix/)
- `git log --name-only --pretty=format: | sort | uniq -c | head -20` — most frequently changed files
- Branching strategy (Git Flow, GitHub Flow, trunk-based)

> **No files are modified during this phase.** I only read.

### Phase A2: Memory Bootstrapping

I analyze your existing codebase to extract initial memories automatically:

**Coding Conventions** (sample analysis):
- Naming conventions (camelCase, snake_case, PascalCase)
- Import/export patterns (named vs default exports)
- Error handling patterns (try/catch, error objects, monads)
- Test patterns (co-located vs separate, describe/it vs test)
- Comment and documentation style (JSDoc, PHPDoc, inline)
- Architecture patterns detected (Repository, Service Layer, MVC, etc.)

**Project Knowledge:**
- Most active areas of the codebase (hot paths)
- External services and integrations detected
- Database access patterns (ORM, raw queries, query builder)
- Authentication and authorization mechanisms

**Reference Memories** (scaffold):
- CI/CD pipelines and their structure
- Deployment targets and environments
- Monitoring and observability setup
- Key documentation pages and wikis

All detected patterns are written as structured memories (not overwritten — appended to existing).

### Phase A3: Diff Plan

I present a detailed diff of what will happen:

```
Changes preview:
─────────────────────────────────────────────────────
📄 FILES TO CREATE (N):
  - memory/reference_ci.md
  - .claude/agents/security-reviewer.md
  - .claude/agents/documentation-writer.md

📝 FILES TO OVERWRITE (N):
  - CLAUDE.md (you have 3 custom rules not in template)
  - AGENTS.md (you have 2 custom agents to preserve)

✅ FILES TO PRESERVE (N):
  - .claude/settings.json (already configured)
  - .claude/agents/backend-dev.md (customized)
  - memory/MEMORY.md (has 5 existing memories)

🔄 MERGE CONFLICTS (if any):
  - AGENTS.md: custom agent "deploy-agent" → keep or drop?
  - CLAUDE.md: section "Testing Policy" → merge or replace?

🧠 MEMORIES TO BOOTSTRAP:
  - 5 coding conventions detected
  - 3 reference memories (CI, deploy, monitoring)
```

Each merge conflict is presented for your decision. With `--dry-run`, nothing is written — just the preview.

### Phase A4: Merge & Write

I write files using intelligent merge rules:

- **`CLAUDE.md`**: Merge sections — keep your existing rules, add template sections that don't conflict. Your custom content is preserved at the top.
- **`AGENTS.md`**: Preserve custom agents (added to taxonomy + flow). Add missing template agents. Keep your existing command paths, guardrails, and conventions.
- **Agent files (`.claude/agents/*.md`)**: For each template agent, check if a custom version exists. If yes, preserve the custom version. If no, add from template.
- **`settings.json`**: Never overwrite if it already exists with content.
- **Memories**: Add bootstrapped memories alongside existing ones. Never remove existing memories.
- **Adjacent configs**: Import relevant rules from Cursor/Copilot configs into the workflow, leaving original files untouched.

### Phase A5: Adoption Report

After merge, I present:

```
✅ Adoption complete!

📊 Summary:
  - 4 files created
  - 2 files merged (custom content preserved)
  - 3 files preserved unchanged
  - 8 memories bootstrapped from code

📋 What changed:
  - CLAUDE.md: +2 sections (Agent Workflow, Memory System)
  - AGENTS.md: +2 agents (security-reviewer, doc-writer)
  - Added 5 convention memories from your code patterns
  - Added 3 reference memories (CI, deploy, monitoring)

🔜 Suggested next steps:
  1. Review the bootstrapped memories for accuracy
  2. Customize the new agents for your domain
  3. Try the workflow on a small feature
```

---

## Mode: `--prune` (Cleanup)

Removes orphaned and stale files left behind as the project evolves. Read-only with `--dry-run` — always preview before deleting.

### What Gets Pruned

| # | Check | Deletes |
|---|---|---|
| P1 | **Orphan Agents** | `.claude/agents/*.md` files not referenced in `AGENTS.md` taxonomy |
| P2 | **Stale Temp Files** | `.claude/workflow-setup/` directory (generated during setup, not needed after) |
| P3 | **Orphan Memories** | Memory files in `memory/` not listed in `MEMORY.md` index |
| P4 | **Empty Dirs** | Empty directories left after pruning |

### Dry-Run (Default Behavior)

With `--prune` alone, I show a preview and ask for confirmation before deleting:

```
🧹 Prune Preview: my-project
─────────────────────────────────────────────────────
🗑️  2 orphan agents (not in AGENTS.md):
      - .claude/agents/old-deploy-agent.md
      - .claude/agents/experimental-scraper.md

🗑️  1 stale temp directory:
      - .claude/workflow-setup/

🗑️  1 orphan memory (not in MEMORY.md):
      - memory/draft-notes.md

📦 Total: 4 items to remove

❓ Proceed with pruning? [y/N]
```

### Force Clean (Skip Confirm)

With `--prune --force`, I skip the confirmation prompt and prune immediately. Use with caution.

### What --prune Does NOT Do

- Does not modify `AGENTS.md`, `CLAUDE.md`, or any active configuration
- Does not remove agents that ARE referenced in `AGENTS.md`
- Does not remove settings, memories that are properly indexed
- Does not touch files outside `.claude/agents/`, `.claude/workflow-setup/`, or `memory/`

---

## What Gets Generated

### CLAUDE.md
Pointer file with project summary, stack info, and links to AGENTS.md and agent configs. In `--adopt` mode, existing rules are preserved and merged.

### AGENTS.md
The orchestration workflow containing:
- 10-agent taxonomy with model tiers
- Orchestration flow (spec-driver → PO → explorer → devs → test → review → security → deploy)
- Parallelization rules and dependencies
- Reviewer decision trees
- Sprint completion checklist

In `--adopt` mode, custom agents are preserved and template agents are added.

### Agent Definitions (`.claude/agents/*.md`)

| Agent | Model | Purpose |
|---|---|---|
| **spec-driver** | deepseek-v4-pro | Spec Driven Development — refines requests into scenarios |
| **product-owner** | deepseek-v4-pro | Creates issues, manages backlog, writes user stories |
| **explorer** | deepseek-v4-flash | Codebase research, architecture mapping, dependency analysis |
| **backend-dev** | qwen3.7-plus | API implementation, business logic, database operations |
| **frontend-dev** | qwen3.7-plus | UI components, state management, API integration |
| **test-writer** | qwen3.7-plus | Unit/integration tests, coverage |
| **code-reviewer** | deepseek-v4-pro | Quality review, bug detection, standards compliance |
| **security-reviewer** | deepseek-v4-pro | Security audit, OWASP Top 10, compliance |
| **documentation-writer** | deepseek-v4-flash | Docs sync, CHANGELOG, ADRs |
| **devops** | qwen3.7-plus | Docker, CI/CD, migrations, deploy |

### Settings
Basic `.claude/settings.json` with default effort level. Never overwritten in `--adopt` mode if already configured.

### Memory
`memory/MEMORY.md` template index with prepared structure for:
- User memories (role/goals)
- Feedback memories (corrections/confirmations)
- Project memories (context/decisions)
- Reference memories (external pointers)

In `--adopt` mode, existing memories are preserved and bootstrapped memories are added.

---

## Reference Files

This skill uses two reference files for stack detection and template generation:

- `references/discovery-patterns.md` — How to detect project stack, commands, and architecture
- `references/stack-patterns.md` — Maps common stacks to placeholder values

---

## Mode: `--health` (Diagnostic)

Diagnoses the workflow setup in any project. Read-only — never modifies files. Can be run at any time, on any project, regardless of whether the workflow is installed.

### Checks Performed

| # | Check | What It Verifies |
|---|---|---|
| H1 | **File Integrity** | `CLAUDE.md`, `AGENTS.md`, `.claude/` dir, `.claude/settings.json`, `memory/MEMORY.md` exist |
| H2 | **Placeholder Audit** | Scans all workflow files for unfilled `{{...}}` placeholders |
| H3 | **Agent Consistency** | All agents in `AGENTS.md` have `.md` files; no orphan agent files; frontmatter valid |
| H4 | **Settings Validation** | `settings.json` is valid JSON; model is known; hooks syntax valid |
| H5 | **Memory Health** | All files listed in `MEMORY.md` exist; no orphan memory files |
| H6 | **Command Dry-Run** | Lint/test/build commands from config are valid (syntax check, not execution) |

### Output Format

```
🏥 Health Report: my-project
─────────────────────────────────────────────────────────────
H1  ✅ File Integrity         — 5/5 files present
H2  ⚠️  Placeholder Audit     — 2 unfilled placeholders found
H3  ❌ Agent Consistency      — 1 agent missing file, 1 orphan
H4  ✅ Settings Validation    — valid JSON, deepseek-v4-flash
H5  ✅ Memory Health          — 3 files, all indexed
H6  ⚠️  Command Dry-Run       — 1 command has invalid path

📊 Overall: 4 passed, 2 warnings, 1 failed

🔧 Recommended actions:
  - Run /init-workflow --adopt to fix placeholder and agent issues
  - Fix settings.json syntax error at line 12
  - Update test command path in AGENTS.md
```

### Severity Levels

| Icon | Meaning |
|---|---|
| ✅ **PASS** | Check passed, no action needed |
| ⚠️ **WARNING** | Non-blocking issue (unfilled placeholder, orphan file) |
| ❌ **FAIL** | Blocking issue (missing critical file, invalid JSON) |

### When to Run

- **After greenfield setup**: verify everything was generated correctly
- **After adoption**: confirm merge didn't break anything
- **Periodically**: catch drift as the project evolves
- **Before major changes**: ensure the workflow is healthy before relying on it

### What --health Does NOT Do

- Does not modify any files
- Does not run lint/test/build commands (only syntax-checks the configured paths)
- Does not require the workflow to be installed (runs on any project)

---

## After Setup (Greenfield) / After Adoption

1. Review the generated/merged files for accuracy
2. Customize agent descriptions for your domain
3. Configure models in your OpenClaude settings or agent frontmatter
4. Start using the workflow for new features
