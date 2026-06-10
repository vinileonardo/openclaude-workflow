# Runbook: Fixing a Bug

> Quick flow: explore → fix → test → review → deploy

## Flow

```
explorer → backend-dev/frontend-dev → test-writer → code-reviewer → devops
```

## Step-by-Step

### 1. Explorer

Investigate the bug before any code is written.

```markdown
@explorer O usuário reportou que ao salvar um rascunho,
o status volta para "pendente" em vez de manter "rascunho".

Investigue:
- Onde o status é alterado no backend
- O fluxo de save vs publish
- Testes existentes para esse comportamento
- Commit history do arquivo suspeito (git blame)

Reporte a causa raiz e os arquivos envolvidos.
```

**Output:** Investigation notes with root cause, affected files, and suggested fix.

### 2. Backend/Frontend Dev

Fix based on investigation.

```markdown
@backend-dev Conforme investigation-notes.md, corrija o bug
no salvamento de rascunhos. A causa raiz é [root cause].

Requisitos:
- Rascunhos mantêm status "draft" até publicação explícita
- Nenhuma regressão no fluxo de publish
- Teste unitário novo para o cenário
```

### 3. Test Writer

```markdown
@test-writer Escreva testes para cobrir o bug corrigido:
- Cenário que reproduzia o bug (regression test)
- Teste de save com status draft
- Teste de publish a partir de draft
```

### 4. Code Reviewer

```markdown
@code-reviewer Revise o fix do bug de rascunho.
```

### 5. DevOps (if hotfix)

```markdown
@devops Este é um hotfix. Faça o deploy para staging,
verifique o healthcheck e avise quando estiver no ar.
```

## Timing

| Step | Who | Est. Time |
|---|---|---|
| 1 | Explorer | 10-20 min |
| 2 | Dev | 15-60 min |
| 3 | Test Writer | 15-30 min |
| 4 | Code Reviewer | 10-15 min |
| 5 | DevOps (if hotfix) | 5-10 min |
