# Runbook: Adding a Feature

> Full pipeline: spec → implementation → tests → review → security → deploy

## Flow

```
spec-driver → product-owner → explorer → backend-dev/frontend-dev → test-writer
                                                                            ↓
                                           security-reviewer ← code-reviewer ←
                                                   ↓
                                               devops
```

## Step-by-Step

### 1. Spec Driver

Invoke with the feature request. Output: `specs/feature-name-sdd.md`

```markdown
@spec-driver Precisamos de uma feature de recuperação de senha.
O usuário esqueceu a senha, clica em "Esqueci minha senha",
recebe um email com link temporário e redefine a senha.
```

**Output:** SDD spec with Given/When/Then scenarios, acceptance criteria, edge cases.

### 2. Product Owner

Invoke with the SDD spec. Output: GitHub issues.

```markdown
@product-owner Com base no spec em specs/recuperacao-senha-sdd.md,
crie as issues necessárias no backlog com prioridades e critérios
de aceitação.
```

### 3. Explorer

Invoke to map the affected code. Output: exploration notes.

```markdown
@explorer Vamos implementar recuperação de senha.
Mapeie:
- Onde ficam as rotas de auth atuais
- Como o envio de email está estruturado
- Tabelas de usuário no banco
- Views/componentes de login existentes
```

### 4. Backend/Frontend Dev

Implement based on spec + exploration notes.

```markdown
@backend-dev Implemente a recuperação de senha conforme
specs/recuperacao-senha-sdd.md e exploration-notes.md.

Endpoint: POST /auth/forgot-password
          POST /auth/reset-password
Email: template de email com link temporário (15 min)
```

```markdown
@frontend-dev Implemente as telas de "Esqueci senha" e
"Redefinir senha" conforme spec e exploration.

Rotas: /forgot-password, /reset-password?token=xxx
```

### 5. Test Writer

```markdown
@test-writer Escreva testes para a feature de recuperação de senha.
- Teste unitário do serviço de reset
- Teste de integração dos endpoints
- Teste de expiração do token
- Teste de email inválido
```

### 6. Code Reviewer

```markdown
@code-reviewer Revise toda a implementação da recuperação de senha.
```

### 7. Documentation Writer (parallel with reviewer)

```markdown
@documentation-writer Atualize a documentação com a nova rota
de recuperação de senha e o fluxo.
```

### 8. Security Reviewer

```markdown
@security-reviewer Faça um security review focado na
recuperação de senha. Atenção a:
- Token expiration e reuse
- Email enumeration (response timing)
- Rate limiting nos endpoints
- Link hijacking
```

### 9. DevOps

```markdown
@devops A feature de recuperação de senha adiciona
2 novos endpoints e um job de email. Valide:
- Se precisa de nova variável de ambiente
- Se o healthcheck continua passando
- DB migration para token expiration index
```

## Timing

| Step | Who | Est. Time |
|---|---|---|
| 1-3 | Spec + PO + Explorer | 15-30 min |
| 4 | Devs | 1-4 hours |
| 5 | Test Writer | 30-60 min |
| 6-7 | Review + Docs (parallel) | 20-30 min |
| 8 | Security Reviewer | 15-20 min |
| 9 | DevOps | 10-15 min |
