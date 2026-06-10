# Runbook: Codebase Audit

> Map the full architecture of a project using the Explorer agent.

## When to Run

- **Onboarding**: new developer learning the codebase
- **Before refactoring**: understand current state before changing
- **Adoption** (`/init-workflow --adopt`): understand what you're adopting
- **Due diligence**: evaluating a project you'll work on

## Step-by-Step

### 1. Project Structure

```markdown
@explorer Mapeie a estrutura completa do projeto.

Liste:
- Diretórios principais e suas responsabilidades
- Quantos arquivos em cada camada (controllers, models, services, views)
- Padrão de organização (feature-first? layer-first?)
- Entry points (index.php, app.js, main.py)
```

### 2. Stack & Dependencies

```markdown
@explorer Analise o stack tecnológico:
- Linguagens e versões
- Framework e versão
- Database e ORM
- Cache, filas, job schedulers
- Bibliotecas externas críticas
- Docker image base
```

### 3. API & Routes

```markdown
@explorer Mapeie todas as rotas/endpoints:
- Tabela de rotas (method, path, controller, middleware)
- Rotas protegidas vs públicas
- Padrão de versionamento de API
- Formato de request/response
```

### 4. Database Schema

```markdown
@explorer Analise o banco de dados:
- Tabelas principais e relacionamentos
- Migrations recentes
- Tamanho aproximado das tabelas
- Índices e consultas mais pesadas
```

### 5. Tests

```markdown
@explorer Analise a cobertura de testes:
- Frameworks de teste
- Quantidade de testes
- O que é testado (unit, integration, e2e)
- CI pipeline de testes
```

### 6. CI/CD & Deploy

```markdown
@explorer Mapeie o pipeline:
- CI provider (GitHub Actions, GitLab CI, etc.)
- Stages do pipeline
- Deploy strategy (blue-green, canary, rolling)
- Ambientes (dev, staging, production)
```

## Output

The explorer should produce a single structured document:

```markdown
# Codebase Audit: [project]

## Overview
[2-3 sentence summary]

## Structure
[directory tree or table]

## Stack
[language, framework, database, infra]

## Architecture
[key patterns, decisions, notable things]

## API
[route table]

## Database
[tables, relationships]

## Testing
[coverage, frameworks, gaps]

## CI/CD
[pipeline stages]

## Hotspots
[files/code areas that change most frequently]
```

## Usage Tips

- Use `--check` during `/init-workflow --adopt` to get a free audit
- Run quarterly on active projects to catch architectural drift
- Save the output as a `reference` memory for future sessions
