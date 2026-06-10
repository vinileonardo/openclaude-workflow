# GitHub Integration Guide

> Optional but comprehensive. Configure GitHub to work seamlessly with the multi-agent workflow: token, projects, milestones, issue hierarchy, Actions, and branch protection.

---

## Quick Decision

```
Quer integração completa?
├── SIM, quero tudo (token + projects + actions + proteção)
│   → Siga o guia completo
├── Só o básico (issues + milestones)
│   → Pule para "Token" + "Milestones"
└── Só CI/CD
    → Pule para "Token" + "GitHub Actions"
```

---

## 1. Token Setup

### Classic Token vs Fine-Grained

| Aspect | Classic Token | Fine-Grained Token |
|---|---|---|
| **Scope control** | Broad (repo, workflow, etc.) | Per-repo, per-permission |
| **Expiry** | Optional (max 1 year recommended) | Required (max 1 year) |
| **Management** | User settings → Developer settings | User settings → Fine-grained tokens |
| **Best for** | Personal projects, automation | Organizations, teams |

### Scopes Necessários

#### Mínimo (issues + milestones)

| Scope | Permissão | Motivo |
|---|---|---|
| `repo` (all) | Full control | Acessar issues, milestones, PRs em repo privado |
| *ou específico:* | | |
| `repo:status` | Read/write | Status de commit |
| `public_repo` | Read/write | Issues em repo público |
| `read:org` | Read only | Ler organization (se aplicável) |

#### Recomendado (projetos + Actions)

| Scope | Permissão | Motivo |
|---|---|---|
| `repo` (all) | Full control | Tudo acima + Actions + Projects |
| `workflow` | Read/write | Disparar GitHub Actions |
| `write:org` | Write | Gerenciar projects da org |
| `read:org` | Read | Ler organization |
| `project` | Read/write | GitHub Projects (kanban) |

#### Avançado (automação total)

| Scope | Permissão | Motivo |
|---|---|---|
| `repo` (all) | Full control | Tudo |
| `workflow` | Read/write | Actions |
| `admin:org` | Admin | Gerenciar org settings |
| `project` | Read/write | Projects |
| `delete_repo` | Admin | Limpeza automatizada |

### Como Criar

1. **GitHub.com** → Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Clique **Generate new token (classic)**
3. Dê um nome descritivo: `openclaude-workflow-token`
4. Selecione os scopes conforme a tabela acima
5. Expiração: **90 dias** (segurança) ou **Custom** (máx 1 ano)
6. Clique **Generate token**
7. **Copie o token imediatamente** — não será mostrado novamente

### Armazenamento Seguro

Nunca hardcode o token no código. Use variável de ambiente:

```bash
# ~/.bashrc ou .env.local
export GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx
export GH_TOKEN=$GITHUB_TOKEN  # GitHub CLI também usa
```

---

## 2. GitHub Projects (Kanban)

### Setup Inicial

1. GitHub.com → seu repo → **Projects** → **Create project**
2. Escolha **Board** (kanban simples) ou **Team planning** (mais recursos)

### Estrutura Recomendada

```
▸ Backlog       — issues não priorizadas, ideias
▸ To Do         — priorizadas para o sprint atual
▸ In Progress   — sendo trabalhadas agora
▸ In Review     — aguardando code review
▸ Done          — concluídas no sprint
```

### Automações

Configure no **Workflows** do project:

| Trigger | Action |
|---|---|
| Issue opened | → **Backlog** |
| Issue assigned | → **To Do** → **In Progress** |
| PR opened linked to issue | → **In Review** |
| PR merged | → **Done** |
| PR closed without merge | → **To Do** |

### Integração com o Workflow

Quando o `product-owner` cria issues, o agente deve:

1. Criar a issue no repo
2. Associar ao project
3. Definir o status inicial baseado no tipo (epic → Backlog, task → To Do)

---

## 3. Milestones

### Como Criar

```bash
# Com GitHub CLI
gh api repos/:owner/:repo/milestones \
  --field title="Sprint 1" \
  --field description="Primeira sprint: auth + users CRUD" \
  --field due_date="2026-06-30"
```

Pelo site: **Issues** → **Milestones** → **Create a milestone**

### Estrutura de Sprint

```
Sprint N | Data: YYYY-MM-DD a YYYY-MM-DD
├── Meta: <objetivo da sprint>
├── Issues associadas (todas as stories/tasks)
└── Progresso: N% (calculado automaticamente)
```

### Boas Práticas

- **1 sprint = 1 milestone**
- Milestones devem ter data de fim
- Use milestones no `product-owner` prompt:

```markdown
@product-owner Crie as issues para a sprint 2
baseadas no backlog. Associe ao milestone "Sprint 2".
```

---

## 4. Issue Hierarchy (Epics, Stories, Tasks)

GitHub não tem hierarquia nativa de issues. Use esta convenção:

### Estrutura

```
Epic (issue with label: epic)
├── Story (issue with label: story, parent via tasklist)
│   ├── Task (issue with label: task, parent via tasklist)
│   ├── Task
│   └── Subtask (checkbox within task issue body)
└── Story
    ├── Task
    └── Task
```

### Labels

Crie estas labels no repo:

| Label | Color | Description |
|---|---|---|
| `epic` | `#5319E7` | Large feature, spans multiple sprints |
| `story` | `#0E8A16` | User-visible feature, fits in one sprint |
| `task` | `#1D76DB` | Technical work item |
| `spike` | `#FBFF38` | Research / exploration |
| `bug` | `#D73A4A` | Bug report |

```bash
# Criar labels via GitHub CLI
gh label create epic --color 5319E7 --description "Large feature, multiple sprints"
gh label create story --color 0E8A16 --description "User-visible feature, one sprint"
gh label create task --color 1D76DB --description "Technical work item"
gh label create spike --color FBFF38 --description "Research / exploration"
gh label create bug --color D73A4A --description "Bug report"
```

### Issue Templates

Crie `.github/ISSUE_TEMPLATE/` no repo para padronizar:

#### `.github/ISSUE_TEMPLATE/epic.md`

```markdown
---
name: Epic
about: Large feature spanning multiple sprints
title: '[Epic] '
labels: epic
assignees: ''
---

## Description
<what this epic encompasses>

## Stories
- [ ] #<issue-number> Story 1
- [ ] #<issue-number> Story 2

## Acceptance Criteria
- [ ] <epic-level criterion>
- [ ] <epic-level criterion>

## Dependencies
- <blocking epics or external factors>
```

#### `.github/ISSUE_TEMPLATE/story.md`

```markdown
---
name: Story
about: User-visible feature, fits in one sprint
title: ''
labels: story
assignees: ''
---

## User Story
As a <role>, I want <action> so that <benefit>.

## Acceptance Criteria
- [ ] <criterion 1>
- [ ] <criterion 2>

## Tasks
- [ ] #<issue-number> Task 1
- [ ] #<issue-number> Task 2

## Technical Notes
<link to SDD spec, exploration notes>
```

#### `.github/ISSUE_TEMPLATE/task.md`

```markdown
---
name: Task
about: Technical work item
title: ''
labels: task
assignees: ''
---

## Description
<what needs to be done>

## Definition of Done
- [ ] Code written
- [ ] Tests passing
- [ ] Code reviewed
- [ ] Deployed

## Technical Context
<relevant files, architecture decisions>
```

#### `.github/ISSUE_TEMPLATE/bug.md`

```markdown
---
name: Bug
about: Report a bug
title: '[Bug] '
labels: bug
assignees: ''
---

## Description
<what happened>

## Steps to Reproduce
1. Go to ...
2. Click ...
3. See error

## Expected Behavior
<what should have happened>

## Environment
- Browser/OS:
- Version/Commit:
- User role:

## Screenshots
<if applicable>
```

### Workflow de Criação

1. `product-owner` analisa SDD spec e estima tamanho
2. Se multi-sprint → cria **epic**
3. Se cabe em 1 sprint → cria **stories** dentro do epic
4. Quebra stories em **tasks**
5. Subtasks são checklists dentro da task (não issues separadas)
6. Tudo associado ao **milestone** da sprint atual

---

## 5. GitHub Actions

### Estrutura Recomendada

```
.github/workflows/
├── ci.yml              # Lint, typecheck, test (toda push)
├── review.yml          # Code review automation
└── deploy.yml          # Deploy (main/staging push)
```

### CI (ci.yml)

```yaml
name: CI

on:
  push:
    branches: [main, staging]
  pull_request:
    branches: [main]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Lint
        run: <lint command>
      - name: Typecheck
        run: <typecheck command>
      - name: Test
        run: <test command>
```

### Review Automation (review.yml)

```yaml
name: Review

on:
  pull_request:
    types: [opened, synchronize, ready_for_review]

jobs:
  label:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/labeler@v5
      
  size:
    runs-on: ubuntu-latest
    steps:
      - uses: pascalgn/size-label-action@v1
        with:
          sizes: >
            {
              "XS": 10,
              "S": 50,
              "M": 200,
              "L": 500,
              "XL": 1000
            }
```

### Deploy (deploy.yml)

```yaml
name: Deploy

on:
  push:
    branches: [staging, main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build
        run: <build command>
      - name: Deploy to ${{ github.ref_name }}
        run: <deploy command>
```

### Actions Secrets

Configure no repo: **Settings** → **Secrets and variables** → **Actions**

| Secret | Purpose |
|---|---|
| `DOCKER_USERNAME` | Registry login |
| `DOCKER_PASSWORD` | Registry password/token |
| `DEPLOY_KEY` | SSH key for deploy server |
| `SLACK_WEBHOOK` | Notifications |

---

## 6. Branch Protection

### Recommended Rules (main branch)

Settings → Branches → Add branch protection rule:

| Rule | Recommended | Why |
|---|---|---|
| Require PR before merge | ✅ | Code review gate |
| Require approvals | ✅ | 1 approval mínimo |
| Dismiss stale reviews | ✅ | Novo commit = nova revisão |
| Require status checks | ✅ | CI must pass |
| Require branches up to date | ✅ | Não mergear com base desatualizada |
| Require conversation resolution | ✅ | Tudo resolvido antes do merge |
| Do not allow bypass | ✅ | Nem admins bypassam |
| Restrict push | ✅ | Só CI/deploy bots |

### Branch Naming Convention

```
feature/<issue-number>-<kebab-description>
fix/<issue-number>-<kebab-description>
hotfix/<issue-number>-<kebab-description>
release/<version>
```

---

## 7. Putting It All Together

### Fluxo Completo

```
Usuário quer nova feature
        │
        ▼
product-owner cria Epic (label: epic, +milestone)
        │
        ▼
product-owner cria Stories (label: story, parent epic via tasklist)
        │
        ▼
product-owner cria Tasks (label: task, parent story via tasklist)
        │
        ▼
Dev cria branch: feature/42-add-login
        │
        ▼
Dev faz PR → CI roda (lint + test)
        │
        ▼
Code review → Security review → PR aprovado
        │
        ▼
Merge → CI roda novamente → Deploy staging
        │
        ▼
Validação → PR main → Deploy production
```

### Comandos Úteis (GitHub CLI)

```bash
# Autenticação
gh auth login

# Criar issue
gh issue create --title "Add login page" --label story \
  --body "As a user, I want to login so that..." \
  --milestone "Sprint 2"

# Listar issues de um milestone
gh issue list --milestone "Sprint 2"

# Ver project boards
gh project list

# Criar release
gh release create v1.0.0 --title "v1.0.0" --notes "Release notes..."
```

### Configuração Rápida (script)

Execute este script no repo para configurar tudo de uma vez:

```bash
#!/bin/bash
# setup-github.sh — Configura labels + templates + branch protection
# Usage: gh repo clone <repo> && cd <repo> && bash setup-github.sh

echo "Creating labels..."
gh label create epic --color 5319E7 --description "Large feature, multiple sprints"
gh label create story --color 0E8A16 --description "User-visible feature, one sprint"
gh label create task --color 1D76DB --description "Technical work item"
gh label create spike --color FBFF38 --description "Research / exploration"
gh label create bug --color D73A4A --description "Bug report"

echo "Creating issue templates..."
mkdir -p .github/ISSUE_TEMPLATE
# ... (copiar templates do doc acima)

echo "Done! Configure branch protection manually via GitHub UI."
```

---

## 8. Como o Workflow se Integra

### Na Skill `/init-workflow`

Se o usuário optar por GitHub integration durante o setup:

1. Prompt para configurar token (ou ler de `GITHUB_TOKEN` env)
2. Criar labels automaticamente (via `gh label create`)
3. Gerar `.github/ISSUE_TEMPLATE/*.md`
4. Gerar `.github/workflows/*.yml`
5. Configurar branch protection (via `gh api`)

Os templates de CI/CD são adaptados ao stack detectado.

### Nos Agentes

- `product-owner`: cria issues, associa milestones, gerencia backlog
- `devops`: cria/atualiza workflows, gerencia secrets, configura protection
- `code-reviewer`: comenta em PRs, adiciona labels (blocking/passing)

### Comandos Rápidos

```bash
# Criar milestone para sprint atual
gh api repos/:owner/:repo/milestones \
  --field title="Sprint N" \
  --field description="..." \
  --field due_date="YYYY-MM-DD"
```
