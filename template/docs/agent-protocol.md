# Agent Communication Protocol

> Defines the contract between agents: what each produces, in what format, and what the next agent consumes.

## Overview

Each agent in the pipeline produces a structured artifact. The next agent reads the previous artifact as context. This ensures no information is lost between steps and the workflow can be resumed at any point.

## Artifact Flow

```
spec-driver ──► sd-spec.md ──► product-owner ──► issues/ ──► explorer
                                                              │
                                                              ▼
                                                   exploration-notes.md
                                                              │
                                          ┌───────────────────┴────────┐
                                          ▼                           ▼
                                    backend-changes.md         frontend-changes.md
                                          │                           │
                                          └───────────────────┬────────┘
                                                              ▼
                                                        test-report.md
                                                              │
                                                    ┌─────────┴─────────┐
                                                    ▼                   ▼
                                              review-report.md    changelog-entry.md
                                                    │                   │
                                                    └─────────┬─────────┘
                                                              ▼
                                                        security-review.md
                                                              │
                                                              ▼
                                                        deploy-check.md
```

---

## Agent Artifacts

### spec-driver → `specs/<feature>-sdd.md`

Produces the SDD specification. This is the foundation document for the entire feature.

```markdown
# SDD: <feature name>

## Context
- **Requester**: <who asked for this>
- **Priority**: <high/medium/low>
- **Estimated complexity**: <S/M/L/XL>

## Business Problem
<what problem is being solved, in business language>

## Scope
- **In scope**: <what this feature covers>
- **Out of scope**: <what is explicitly NOT covered>

## Scenarios

### Scenario: <happy path>
  Given <precondition>
  When  <action>
  Then  <expected result>

### Scenario: <edge case>
  Given <precondition>
  When  <action>
  Then  <expected result>

### Scenario: <error case>
  Given <precondition>
  When  <action>
  Then  <error expected>

## Technical Constraints
<known constraints, architecture decisions>

## Open Questions
<what still needs to be decided>
```

**Consumed by:** product-owner, explorer, devs, test-writer

---

### product-owner → `issues/` or backlog

Produces structured issues. If the project uses GitHub Issues, creates actual issues. Otherwise, a local file.

```markdown
---
title: "<feature> — <specific task>"
priority: <P0/P1/P2>
story_points: <1/2/3/5/8>
---

## User Story
As a <role>, I want <action> so that <benefit>.

## Acceptance Criteria
- [ ] <criterion 1>
- [ ] <criterion 2>

## Technical Notes
<link to sd-spec.md, link to relevant files>

## Dependencies
- <issue # or "none">
```

**Consumed by:** explorer

---

### explorer → `docs/exploration-notes.md`

Produces a codebase map. Written once per feature or per session.

```markdown
# Exploration: <feature>

## Files Affected

| File | Role | Estimated Change |
|---|---|---|
| <path> | <controller/model/view> | <new/modified/deleted> |

## Architecture Map
<how the relevant code is organized, what each layer does>

## Key Findings
- <architecture decision or pattern to follow>
- <potential pitfalls>
- <existing similar features to reference>

## Data Flow
<how data moves through the system for this feature>

## Dependencies
<external services, packages, or teams involved>
```

**Consumed by:** backend-dev, frontend-dev

---

### backend-dev → `docs/backend-changes.md`

Produces a summary of backend changes after implementation.

```markdown
# Backend Changes: <feature>

## New Endpoints

| Method | Path | Controller | Auth | Validation |
|---|---|---|---|---|
| POST | /api/<resource> | <Controller@method> | <JWT/api-key/none> | <rules> |

## Modified Endpoints

| Method | Path | What Changed |
|---|---|---|
| PUT | /api/<resource> | <description> |

## Database Changes
- <migration description>
- <new table, column, or index>

## Configuration
- <new env vars, if any>
- <new config options, if any>

## Test Coverage
- <number and type of tests added>
- <test file locations>
```

**Consumed by:** test-writer (via files changed), code-reviewer

---

### frontend-dev → `docs/frontend-changes.md`

Produces a summary of frontend changes after implementation.

```markdown
# Frontend Changes: <feature>

## New Components

| Component | Route | State | Description |
|---|---|---|---|
| <Name> | <path> | <local/global> | <purpose> |

## Modified Components

| Component | What Changed |
|---|---|
| <Name> | <description> |

## API Integration
- <new API calls>
- <data transformation details>

## States Covered
- [ ] Loading
- [ ] Empty
- [ ] Error
- [ ] Success
- [ ] Edge cases

## Test Coverage
- <number and type of tests>
```

**Consumed by:** test-writer, code-reviewer

---

### test-writer → `docs/test-report.md`

Produces a test report after writing tests.

```markdown
# Test Report: <feature>

## Summary
- **Tests written**: <N>
- **Unit**: <N>
- **Integration**: <N>
- **Coverage areas**: <list>

## Test Cases

### <area>
- `test_<scenario>` — <what it validates>
- `test_<scenario>` — <edge case covered>

## Running

```bash
<command to run the new tests>
```

## Gaps
- <areas not covered, if any>
```

**Consumed by:** code-reviewer

---

### code-reviewer → `docs/review-report.md`

Produces a structured code review report.

```markdown
# Code Review: <feature>

## Reviewed Files
- <file> — <lines changed>
- <file> — <lines changed>

## Verdict
**PASS** / **PASS WITH NOTES** / **BLOCKING**

## Findings

### Backend
- [ ] <finding> — severity, file:line

### Frontend
- [ ] <finding> — severity, file:line

### General
- [ ] <finding> — severity, file:line

## Summary
<overall assessment, ~3 sentences>
```

**Consumed by:** security-reviewer, devops

---

### documentation-writer → `docs/changelog-entry.md`

Produces documentation updates and changelog entry.

```markdown
# Documentation: <feature>

## What Changed
<user-facing description of what was added/changed>

## Changelog Entry

```markdown
### Added
- <feature>: <brief description>

### Changed
- <component>: <what changed>

### Fixed
- <bug>: <description>
```

## Files Updated
- <doc file> — <what was updated>
```

**Consumed by:** devops (for release notes)

---

### security-reviewer → `docs/security-review.md`

Produces a security audit report.

```markdown
# Security Review: <feature>

## Scope
<endpoints, components, or areas reviewed>

## Summary
**PASS** / **PASS WITH NOTES** / **BLOCKING**

## Findings

| # | Severity | Description | File | Recommendation |
|---|---|---|---|---|
| 1 | HIGH | <issue> | <path> | <fix> |
| 2 | MEDIUM | <issue> | <path> | <fix> |

## Checklist
- [ ] Authentication
- [ ] Authorization
- [ ] Input validation
- [ ] Output encoding
- [ ] Rate limiting
- [ ] CSRF
- [ ] Secure headers
- [ ] Secrets
- [ ] Audit logging

## Verdict
<blocking or not, conditions to resolve>
```

**Consumed by:** devops (blocking gate)

---

### devops → `docs/deploy-check.md`

Produces a deployment readiness check.

```markdown
# Deploy Check: <feature>

## Pre-deploy
- [ ] Database migrations reviewed
- [ ] Environment variables configured
- [ ] Dependencies updated
- [ ] Healthcheck passes locally

## Rollout Plan
<blue-green / rolling / recreate>

## Rollback Plan
<steps to revert>

## Monitoring
- <what metrics to watch after deploy>
- <what logs to check>

## Healthcheck
```bash
<curl or command to verify>
```

## Deploy Verdict
**READY** / **BLOCKED** (reason)
```

**Consumed by:** nobody — this is the final artifact.

---

## Context Passing Rules

1. **Each agent starts** by reading the previous artifact (or being told where it is)
2. **Each agent ends** by writing its artifact to `docs/`
3. **Artifacts accumulate** — no artifact is deleted during a feature flow
4. **Skip artifact**: if an agent produces no changes (e.g., security-review passes with no findings), write a minimal artifact stating "No issues found"
5. **Resuming**: any agent can resume the pipeline by reading the artifact chain from `sd-spec.md` forward

## File Conventions

- All artifacts go in `<project>/docs/` with kebab-case names
- Artifact files are committed alongside code (PR documentation trail)
- If a file already exists, append a new section — never overwrite
