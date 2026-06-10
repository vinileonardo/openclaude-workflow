# Stack Patterns Reference

Maps common project stacks to placeholder values used by the template system.

## PHP + Laravel

| Placeholder | Value |
|---|---|
| BACKEND_LANGUAGE | PHP 8.3 |
| BACKEND_FRAMEWORK | Laravel 11 |
| BACKEND_STRICT_MODE | declare(strict_types=1) |
| BACKEND_TEST_FRAMEWORK | PHPUnit |
| BACKEND_DIR | ./ |
| BACKEND_ENTRY_POINT | public/index.php |
| BACKEND_ROUTES_DIR | routes/ |
| BACKEND_LINT_CMD | ./vendor/bin/pint |
| BACKEND_TYPECHECK_CMD | ./vendor/bin/phpstan analyse |
| BACKEND_TEST_CMD | ./vendor/bin/phpunit |
| BACKEND_DATABASE_PATTERN | Eloquent ORM (parameterized) |

## PHP + Slim Framework

| Placeholder | Value |
|---|---|
| BACKEND_LANGUAGE | PHP 8.3 |
| BACKEND_FRAMEWORK | Slim 4 |
| BACKEND_STRICT_MODE | declare(strict_types=1) |
| BACKEND_TEST_FRAMEWORK | PHPUnit |
| BACKEND_DIR | backend/ |
| BACKEND_ENTRY_POINT | public/index.php |
| BACKEND_ROUTES_DIR | src/routes/ |
| BACKEND_LINT_CMD | ./vendor/bin/phpcs |
| BACKEND_TYPECHECK_CMD | ./vendor/bin/phpstan analyse --level=8 |
| BACKEND_TEST_CMD | ./vendor/bin/phpunit |

## Node.js + Express

| Placeholder | Value |
|---|---|
| BACKEND_LANGUAGE | Node.js 22 |
| BACKEND_FRAMEWORK | Express |
| BACKEND_TEST_FRAMEWORK | Jest / Vitest |
| BACKEND_DIR | server/ or ./ |
| BACKEND_ENTRY_POINT | src/index.js |
| BACKEND_ROUTES_DIR | src/routes/ |
| BACKEND_LINT_CMD | npm run lint |
| BACKEND_TYPECHECK_CMD | npx tsc --noEmit |
| BACKEND_TEST_CMD | npm test |
| BACKEND_DATABASE_PATTERN | Parameterized queries / ORM |

## Node.js + Next.js

| Placeholder | Value |
|---|---|
| FRONTEND_FRAMEWORK | Next.js 15 |
| FRONTEND_BUILD_TOOL | Turbopack / Webpack |
| FRONTEND_LANGUAGE | TypeScript |
| FRONTEND_TEST_FRAMEWORK | Vitest / Playwright |
| FRONTEND_DIR | ./ |
| BACKEND_LANGUAGE | Node.js 22 (API routes) |
| FRONTEND_LINT_CMD | npm run lint |
| FRONTEND_TYPECHECK_CMD | npx tsc --noEmit |
| FRONTEND_TEST_CMD | npm test |
| FRONTEND_BUILD_CMD | npm run build |

## Python + Django

| Placeholder | Value |
|---|---|
| BACKEND_LANGUAGE | Python 3.12 |
| BACKEND_FRAMEWORK | Django 5 |
| BACKEND_STRICT_MODE | mypy strict |
| BACKEND_TEST_FRAMEWORK | pytest / Django TestCase |
| BACKEND_DIR | ./ |
| BACKEND_ENTRY_POINT | manage.py |
| BACKEND_ROUTES_DIR | urls.py |
| BACKEND_LINT_CMD | ruff check . |
| BACKEND_TYPECHECK_CMD | mypy . --strict |
| BACKEND_TEST_CMD | pytest |
| DATABASE | PostgreSQL / SQLite |
| CACHE | Redis |

## Python + FastAPI

| Placeholder | Value |
|---|---|
| BACKEND_LANGUAGE | Python 3.12 |
| BACKEND_FRAMEWORK | FastAPI |
| BACKEND_TEST_FRAMEWORK | pytest + httpx |
| BACKEND_DIR | app/ or ./ |
| BACKEND_ENTRY_POINT | main.py |
| BACKEND_ROUTES_DIR | app/routes/ |
| BACKEND_LINT_CMD | ruff check . |
| BACKEND_TYPECHECK_CMD | mypy . |
| BACKEND_TEST_CMD | pytest |

## Go

| Placeholder | Value |
|---|---|
| BACKEND_LANGUAGE | Go 1.23 |
| BACKEND_FRAMEWORK | net/http / Gin / Chi |
| BACKEND_STRICT_MODE | go vet |
| BACKEND_TEST_FRAMEWORK | go test |
| BACKEND_DIR | ./ |
| BACKEND_ENTRY_POINT | cmd/server/main.go |
| BACKEND_ROUTES_DIR | internal/handler/ |
| BACKEND_LINT_CMD | golangci-lint run |
| BACKEND_TYPECHECK_CMD | go vet ./... |
| BACKEND_TEST_CMD | go test ./... |

## Frontend Only (React + Vite)

| Placeholder | Value |
|---|---|
| FRONTEND_FRAMEWORK | React 19 |
| FRONTEND_BUILD_TOOL | Vite |
| FRONTEND_LANGUAGE | TypeScript |
| FRONTEND_STATE_MANAGEMENT | Zustand / Redux / Context |
| FRONTEND_DATA_FETCHING | TanStack Query / SWR |
| FRONTEND_ROUTING | React Router v7 |
| FRONTEND_STYLING | Tailwind CSS / CSS Modules |
| FRONTEND_TEST_FRAMEWORK | Vitest + Testing Library |
| FRONTEND_LINT_CMD | npm run lint |
| FRONTEND_TYPECHECK_CMD | npx tsc --noEmit |
| FRONTEND_TEST_CMD | npm test |
| FRONTEND_BUILD_CMD | npm run build |

## Monorepo (Turborepo / Nx)

| Placeholder | Value |
|---|---|
| BACKEND_DIR | apps/api/ or packages/backend/ |
| FRONTEND_DIR | apps/web/ or packages/frontend/ |
| BACKEND_LINT_CMD | npm run lint --filter=backend |
| FRONTEND_LINT_CMD | npm run lint --filter=frontend |
| BACKEND_TEST_CMD | npm run test --filter=backend |
| FRONTEND_TEST_CMD | npm run test --filter=frontend |
| FRONTEND_BUILD_CMD | npm run build --filter=frontend |

## Docker Placeholder Patterns

| If detected | Suggestion |
|---|---|
| docker-compose.yml with `php` service | CONTAINER_PREFIX = project_php |
| docker-compose.yml with `node` service | CONTAINER_PREFIX = project_node |
| docker-compose.yml with `python` service | CONTAINER_PREFIX = project_python |
| Multi-service compose | DOCKER_SERVICES = web, api, db, cache |
| Production Dockerfile | DOCKER_BUILD_CMD = docker build -t project:latest . |

## Package Manager Detection

| Lock file | Package Manager | Update Command |
|---|---|---|
| package-lock.json | npm | npm install |
| yarn.lock | yarn | yarn install |
| pnpm-lock.yaml | pnpm | pnpm install |
| bun.lockb | bun | bun install |
| composer.lock | Composer | composer install |
| poetry.lock | Poetry | poetry install |
| go.sum | Go modules | go mod tidy |
| Cargo.lock | Cargo | cargo build |
