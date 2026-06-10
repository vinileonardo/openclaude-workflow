# Runbook: First Task (Workflow Validation)

> A "hello world" task to validate that the entire workflow is set up correctly.
> Add a `GET /health` endpoint that returns `{ "status": "ok" }`.

## Why This Task

- Touches every agent in the pipeline
- Zero business complexity — focus is on the workflow, not the code
- If something is broken (wrong command paths, missing configs), you catch it now
- Verifies that agents can pass context between each other

## Expected Duration

15-30 minutes for the full pipeline.

---

## Step-by-Step

### 1. Spec Driver

```markdown
@spec-driver Precisamos de um endpoint GET /health que retorne
{ "status": "ok" } com HTTP 200. Isso é usado pelo healthcheck
do deploy e pelo orchestrador de containers.
```

**Expected artifact:** `specs/health-endpoint-sdd.md`

### 2. Product Owner

```markdown
@product-owner Crie uma issue para o endpoint de health
baseada no spec specs/health-endpoint-sdd.md.
```

**Expected artifact:** issue criada (ou `issues/health-endpoint.md`)

### 3. Explorer

```markdown
@explorer Precisamos adicionar um endpoint GET /health.
Mapeie:
- Onde as rotas são registradas
- O formato de response usado nos endpoints existentes
- Se já existe algum middleware de healthcheck
- Onde os testes de integração ficam
```

**Expected artifact:** `docs/exploration-notes.md`

### 4. Backend Dev

```markdown
@backend-dev Implemente GET /health conforme o spec.

Output:
{ "status": "ok" }

Headers:
Content-Type: application/json
Cache-Control: no-cache

Requisitos:
- Não requer autenticação
- Não faz query no banco
- HTTP 200 sempre
- Rota pública
```

**Expected artifact:** `docs/backend-changes.md`

### 5. Test Writer

```markdown
@test-writer Escreva testes para o endpoint /health:
- Teste de response HTTP 200
- Teste de body (content-type JSON, status field)
- Teste sem autenticação (rota pública)
```

**Expected artifact:** `docs/test-report.md`

### 6. Code Reviewer

```markdown
@code-reviewer Revise a implementação do healthcheck.
```

**Expected artifact:** `docs/review-report.md`

### 7. Documentation Writer

```markdown
@documentation-writer Atualize a documentação com:
- O novo endpoint /health na lista de rotas
- Entry no CHANGELOG como "Added"
```

**Expected artifact:** `docs/changelog-entry.md`

### 8. Security Reviewer

```markdown
@security-reviewer Revise o endpoint /health:
- Informação disponível para usuários não autenticados
- Rate limiting (se aplicável)
- Se expõe informação interna desnecessária
```

**Expected artifact:** `docs/security-review.md`

### 9. DevOps

```markdown
@devops Valide que o healthcheck /health funciona no deploy:
- Healthcheck do container aponta para /health?
- Readiness probe configurada?
- Porta exposta corretamente?

Depois de validado, faça o deploy.
```

**Expected artifact:** `docs/deploy-check.md`

---

## Validation

After the pipeline completes, verify:

```bash
# Local smoke test
curl http://localhost:8080/health
# → {"status":"ok"}

# Run all tests
<project test command>
```

If everything passes, the workflow is fully operational.

## Troubleshooting

| Step | Failure | Likely Cause | Fix |
|---|---|---|---|
| 4 | Route not found | Wrong route file path | Check exploration notes, update AGENTS.md paths |
| 5 | Test fails | Test framework mismatch | Update test command in AGENTS.md |
| 6 | Reviewer can't find code | Wrong file paths | Update agent's project context |
| 9 | Healthcheck fails | Port mismatch | Align dev port with container port |
