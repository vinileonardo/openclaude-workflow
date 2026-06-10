# Runbook: Security Review

> Standalone security audit of a feature, endpoint, or whole module.

## Flow

```
explorer (optional) → security-reviewer
```

## When to Run

- Before every production release
- After auth-related changes
- After adding new dependencies
- At least once per sprint

## Step-by-Step

### Option A: Full Context (with Explorer)

```markdown
@explorer Mapeie todos os endpoints de autenticação e
autorização para o security-reviewer:
- Rotas protegidas vs públicas
- Middleware de auth
- Fluxo de tokens (emissão, refresh, expiração)
- Roles e permissões
- Rate limiting existente
```

Then pass to the reviewer:

```markdown
@security-reviewer Faça um security review completo nos
endpoints de auth. Baseie-se no exploration-notes.md.

Foco em:
- OWASP Top 10 (broken auth, injection, XSS, CSRF)
- Token handling (storage, expiration, rotation)
- Rate limiting e brute force protection
- Input validation
- Error messages (information disclosure)
- Session management
```
git
### Option B: Direct Review

When the scope is clear, invoke the reviewer directly.

```markdown
@security-reviewer Revise o novo endpoint POST /api/transfer
para segurança:

Contexto: transferência entre contas, autenticada via JWT.

Verifique:
- Authorization: usuário só transfere da própria conta
- Input: valor positivo, conta destino válida
- Rate limiting: quantas transferências por minuto
- Idempotency: mesma requisição não executa duas vezes
- CSRF: endpoint com side effect tem proteção?
- Audit logging: quem transferiu, quanto, quando
```

## Review Output

The security reviewer must produce a structured report:

```markdown
## Security Review: [scope]

### Summary
[PASS / PASS WITH NOTES / BLOCKING]

### Findings

| # | Severity | Description | File | Fix |
|---|---|---|---|---|
| 1 | HIGH | No rate limiting on transfer | routes/transfer.php | Add limiter |
| 2 | MEDIUM | Error exposes SQL error | TransferController.php | Sanitize |

### Recommendations
[actionable list]

### Blocking?
[YES / NO — must be resolved before merge]
```

## Checklist

- [ ] Authentication (all endpoints that need it)
- [ ] Authorization (user can only access own resources)
- [ ] Input validation (type, length, format, range)
- [ ] Output encoding (XSS prevention)
- [ ] Error handling (no stack traces, no info disclosure)
- [ ] Rate limiting (login, transfer, any write endpoint)
- [ ] CSRF protection (all state-changing endpoints)
- [ ] Secure headers (CSP, HSTS, X-Frame-Options)
- [ ] Dependency check (known CVEs in new deps)
- [ ] Secrets (no keys, tokens, passwords in code)
- [ ] Logging (audit trail for sensitive operations)
- [ ] Session management (timeout, reuse, revocation)
