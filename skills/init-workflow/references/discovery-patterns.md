# Discovery Patterns Reference

This document defines how to detect project stack, commands, and architecture during Phase 1 (Discovery).

## Config File Detection

### Node.js / TypeScript

| File | Signals |
|---|---|
| `package.json` | Node.js project. Check `dependencies` and `devDependencies`. |
| `tsconfig.json` | TypeScript enabled |
| `next.config.js` / `next.config.ts` | Next.js |
| `vite.config.ts` | Vite |
| `astro.config.mjs` | Astro |
| `turbo.json` | Turborepo monorepo |
| `nx.json` | Nx monorepo |

**Extract from `package.json`:**
- `scripts.lint` → `{{FRONTEND_LINT_CMD}}`
- `scripts.test` → `{{FRONTEND_TEST_CMD}}`
- `scripts.build` → `{{FRONTEND_BUILD_CMD}}`
- `scripts.typecheck` or `scripts.tsc` → `{{FRONTEND_TYPECHECK_CMD}}`
- `scripts.dev` → dev command
- Detect framework from `dependencies`: react, vue, next, svelte, angular, solid
- Detect test framework: vitest, jest, mocha, cypress, playwright

### PHP

| File | Signals |
|---|---|
| `composer.json` | PHP project |
| `composer.lock` | Dependency versions locked |

**Extract from `composer.json`:**
- `scripts.lint` → `{{BACKEND_LINT_CMD}}`
- `scripts.test` → `{{BACKEND_TEST_CMD}}`
- `scripts.analyze` or `scripts.phpstan` → `{{BACKEND_TYPECHECK_CMD}}`
- Detect framework from `require`: slim/slim, laravel/framework, symfony/symfony
- Detect test framework: phpunit, pest, codeception

### Python

| File | Signals |
|---|---|
| `requirements.txt` | Python dependencies |
| `pyproject.toml` | Modern Python project (PEP 621) |
| `Pipfile` | Pipenv |
| `poetry.lock` | Poetry |
| `manage.py` | Django |
| `app.py` / `main.py` | Flask / FastAPI |

**Extract from `pyproject.toml`:**
- `[tool.pytest.ini_options]` → pytest
- `[tool.black]` → black formatter
- `[tool.ruff]` → ruff linter
- Detect framework: django, flask, fastapi, starlette

### Go

| File | Signals |
|---|---|
| `go.mod` | Go module (read module name) |
| `go.sum` | Dependency checksums |
| `main.go` | Entry point |
| `cmd/` | Command structure (common pattern) |
| `internal/` | Internal package pattern |

### Rust

| File | Signals |
|---|---|
| `Cargo.toml` | Rust project |
| `Cargo.lock` | Dependency lock |
| `src/main.rs` | Binary entry |
| `src/lib.rs` | Library entry |

### Java / Kotlin

| File | Signals |
|---|---|
| `pom.xml` | Maven |
| `build.gradle` / `build.gradle.kts` | Gradle |
| `settings.gradle` | Gradle settings |

### Docker

| File | Signals |
|---|---|
| `docker-compose.yml` / `.yaml` | Docker Compose |
| `Dockerfile` | Docker image build |
| `Dockerfile.*` | Multi-environment Dockerfiles |

**Extract from `docker-compose.yml`:**
- Service names → `{{CONTAINER_PREFIX}}`
- Port mappings → `{{PORTS}}`
- Environment variables → env config
- Image names → deployment info
- Volume mounts → persistence setup

## Directory Structure Detection

### Backend Patterns

Look for these directories and detect the first one that exists:
```
backend/       # Common pattern
server/        # Node.js/Go common
api/           # API-only projects
src/           # When src contains backend code (check for PHP/JS files)
app/           # Python/Django common
lib/           # Library code
cmd/           # Go cmd pattern
```

### Frontend Patterns

Look for these directories:
```
frontend/          # Common
frontend_react/    # React explicit
web/               # Web-specific
app/               # App router / Next.js
client/            # Client-side code
ui/                # UI package
src/               # When src contains frontend code
```

### Test Patterns

```
tests/               # Backend/integration tests
__tests__/           # Frontend tests
spec/                # Spec files
*.test.tsx           # Test files co-located
*.spec.ts            # Spec files co-located
```

## Architecture Detection

### State Management

Look in `package.json` dependencies:
- `zustand` → Zustand
- `@reduxjs/toolkit` → Redux
- `pinia` → Pinia (Vue)
- `vuex` → Vuex
- `jotai` / `recoil` → Atomic state
- `react-query` / `@tanstack/react-query` → TanStack Query
- `swr` → SWR
- `apollo-client` → Apollo (GraphQL)

### API Layer

Look for:
- `axios` / `fetch` wrappers → REST
- `graphql` / `apollo` → GraphQL
- `trpc` / `@trpc/client` → tRPC
- `openapi` / `swagger` → OpenAPI docs

### Auth

Look for:
- `jsonwebtoken` / `jwt` → JWT
- `passport` → Passport.js
- `next-auth` / `next-auth/react` → NextAuth
- `@supabase/supabase-js` → Supabase Auth
- `firebase/auth` → Firebase Auth
- `session` middleware → Session-based

### Database

Look for in config files and imports:
- `mysql` / `mysql2` → MySQL
- `pg` / `postgres` → PostgreSQL
- `mongodb` / `mongoose` → MongoDB
- `sqlite3` → SQLite
- `redis` / `ioredis` → Redis
- `prisma` → Prisma ORM
- `drizzle-orm` → Drizzle ORM
- `typeorm` → TypeORM
- `sequelize` → Sequelize
- `knex` → Knex

### Package Manager

Detect from lock files:
- `package-lock.json` → npm
- `yarn.lock` → yarn
- `pnpm-lock.yaml` → pnpm
- `bun.lockb` → bun
