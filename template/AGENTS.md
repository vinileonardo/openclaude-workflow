# AGENTS.md

> Full orchestration workflow for multi-agent AI development with OpenClaude.
> Customize the `{{PLACEHOLDERS}}` for your project stack.

## Placeholders

Before using, replace:

| Placeholder | Description |
|---|---|
| `{{PROJECT_NAME}}` | Your project name |
| `{{STACK_SUMMARY}}` | e.g. "React 19 + Node.js + PostgreSQL" |
| `{{BACKEND_LANGUAGE}}` | e.g. "PHP 8.3", "Node.js", "Python 3.12" |
| `{{BACKEND_FRAMEWORK}}` | e.g. "Slim 4", "Express", "FastAPI" |
| `{{FRONTEND_LANGUAGE}}` | e.g. "TypeScript", "JavaScript" |
| `{{FRONTEND_FRAMEWORK}}` | e.g. "React 19", "Vue 3", "Next.js" |
| `{{DATABASE}}` | e.g. "MySQL", "PostgreSQL", "MongoDB" |
| `{{CACHE}}` | e.g. "Redis", "Memcached" |
| `{{BACKEND_DIR}}` | e.g. "backend/", "server/", "api/" |
| `{{FRONTEND_DIR}}` | e.g. "frontend/", "web/", "app/" |

## Agent Taxonomy

| Agent | Model | Tools | Permission | Role |
|---|---|---|---|---|
| **spec-driver** | deepseek-v4-pro | Read, Grep, Glob | plan | Refines prompts into SDD specs |
| **product-owner** | deepseek-v4-pro | Read, Grep, Glob | plan | Creates issues, manages backlog |
| **explorer** | deepseek-v4-flash | Read, Grep, Glob | plan | Codebase research, mapping |
| **backend-dev** | qwen3.7-plus | All + Bash | acceptEdits | API, business logic, DB |
| **frontend-dev** | qwen3.7-plus | All + Bash | acceptEdits | UI, components, state |
| **test-writer** | qwen3.7-plus | All + Bash | acceptEdits | Unit/integration tests |
| **code-reviewer** | deepseek-v4-pro | Read, Grep, Glob | plan | Quality review, bug detection |
| **security-reviewer** | deepseek-v4-pro | Read, Grep, Glob | plan | Security audit, OWASP |
| **documentation-writer** | deepseek-v4-flash | Read, Grep, Glob | plan | Docs sync, CHANGELOG, ADRs |
| **devops** | qwen3.7-plus | All + Bash | acceptEdits | Docker, CI/CD, deploy |

## Model Guide

| Model | Purpose | Agents | Cost |
|---|---|---|---|
| **deepseek-v4-flash** | Fast, read-only, cheap | Explorer, Doc Writer | Low |
| **qwen3.7-plus** | Implementation, coding | Backend/Frontend/Test/DevOps | Medium |
| **deepseek-v4-pro** | Deep thinking, review | Spec Driver, PO, Code/Security Review | Higher |

## Orchestration Flow

```
Spec Driver → Product Owner → Explorer → Devs → Test Writer → Code Reviewer + Doc Writer → Security Reviewer → DevOps
```

### Dependencies

- Spec Driver → BEFORE Product Owner
- Product Owner → BEFORE any implementation
- Explorer → BEFORE devs begin
- Code Reviewer → AFTER all code/test done
- Security Reviewer → AFTER Code Reviewer (last gate)
- Documentation Writer → IN PARALLEL with Code/Security Review
- DevOps → AFTER Code and Security approved

### Parallelization

- Backend + Frontend devs can work in parallel (if independent)
- Test Writer can run in parallel with Code Reviewer (if no dependency)
- Documentation Writer always runs in parallel (never blocks)

## Agent Descriptions

### 0. Spec Driver

Always starts the flow for non-trivial tasks. Refines requests into precise specifications before any planning or implementation.

- Analyzes request, decomposes into Given/When/Then scenarios
- Identifies ambiguities, blind spots, edge cases
- Maps impact (files, layers, risks)
- Defines testable acceptance criteria
- Estimates complexity and documents trade-offs

**Skip when:** simple questions, typos, exploratory debugging, one-liner tasks.

### 1. Product Owner

Transforms specifications into GitHub/GitLab issues.

- Creates detailed issues with clear acceptance criteria
- Breaks into technical tasks when needed
- Prioritizes and identifies dependencies
- Creates milestones for related issues
- Returns issue IDs when done

**Skip when:** codebase questions, trivial tasks, exploratory debugging.

### 2. Explorer

Maps the codebase before implementation.

- Finds relevant files, patterns, conventions
- Documents discoveries for devs
- Maps dependencies and data flow

### 3. Backend Dev

Implements API/backend code.

- Consults docs and relevant files
- Follows \{{BACKEND_CODING_STANDARDS\}}
- Validates with `\{{BACKEND_LINT_CMD\}}` and `\{{BACKEND_TYPECHECK_CMD\}}`
- Writes tests if complex

### 4. Frontend Dev

Implements UI/frontend code.

- Consults related components and design
- Follows \{{FRONTEND_LANGUAGE_MODE\}} standards
- Validates with `\{{FRONTEND_TYPECHECK_CMD\}}` and `\{{FRONTEND_LINT_CMD\}}`
- Integrates with backend via data layer

### 5. Test Writer

Writes/updates tests.

- \{{BACKEND_TEST_FRAMEWORK\}} for backend, \{{FRONTEND_TEST_FRAMEWORK\}} for frontend
- Covers happy path, errors, edge cases
- Validates all tests pass

### 6. Code Reviewer

Reviews and validates before finalizing.

- Verifies acceptance criteria met
- Checks security, performance, standards
- Validates with lint/typecheck/test
- Approves or requests changes

### 7. Security Reviewer

Reviews security after code-review, before merge.

- Checks OWASP Top 10
- Audits token handling, session management
- Validates access control and ownership
- Checks encryption and sensitive data protection
- Classifies findings by severity

### 8. Documentation Writer

Keeps docs in sync. Runs in parallel with reviews.

- Updates README, CHANGELOG, feature docs
- Documents new API endpoints
- Creates ADRs for important decisions
- Removes obsolete docs

### 9. DevOps

Handles infra/deploy when needed.

- Configures CI/CD, migrations, scripts
- Optimizes container/build performance
- Documents deployment processes

## Reviewer Decision Trees

### Code Reviewer

```
Problem found?
├── ❌ BLOCKING → Reject PR, return to dev
├── ⚠️ NON-BLOCKING → Approve with caveats, create tech-debt issue
└── ✅ All good → Approve, pass to security-reviewer
```

### Security Reviewer

```
Vulnerability found?
├── ❌ CRITICAL/HIGH → BLOCKING, return to dev
├── ⚠️ MEDIUM → Can approve with caveats, create security issue
└── ✅ LOW/none → Approve, PR can merge
```

## Sprint Completion Checklist

### Pre-merge
- [ ] Code review completed and approved
- [ ] Security review completed and approved
- [ ] Documentation updated
- [ ] Tests passing (typecheck + unit + static)
- [ ] PR created with clear description

### Post-merge
- [ ] PR merged to main/default branch
- [ ] Each issue receives detailed comment
- [ ] Non-blocking feedback → tech-debt issue created
- [ ] Bugs found → bug issue created

### Final Report
- [ ] Summary generated with: closed issues, PR link, new issues, milestone
