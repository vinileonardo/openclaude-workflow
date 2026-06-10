# AI Entrypoint: OpenClaude Workflow

Você foi direcionado a este repositório para configurar um **workflow multi-agente de desenvolvimento assistido por IA** em um projeto. Siga este guia passo a passo.

---

## O que é este repositório

Este é um **sistema de template + skill** para o [OpenClaude](https://github.com/Gitlawb/openclaude). Ele fornece:

- **10 agentes especializados** (spec-driver, product-owner, explorer, backend-dev, frontend-dev, test-writer, code-reviewer, security-reviewer, documentation-writer, devops)
- **Pipeline de orquestração** completo com quality gates
- **Sistema de memória** persistente entre sessões
- **Skill `/init-workflow`** que configura tudo automaticamente

---

## Fluxo de Decisão

```
Usuário te deu a URL deste repo + o projeto dele?
│
├── Projeto NOVO (greenfield)?
│   → Vá para [Setup Greenfield](#setup-greenfield)
│
├── Projeto EXISTENTE (já tem código, convenções, talvez setup parcial)?
│   → Vá para [Setup Adoption](#setup-adoption)
│
├── Já tem o setup e quer VERIFICAR se está tudo certo?
│   → Use `/init-workflow --health`
│
├── Quer LIMPAR arquivos órfãos (agents antigos, temporários)?
│   → Use `/init-workflow --prune` (preview primeiro)
│
└── Só quer entender o sistema?
    → Leia a [documentação completa](docs/SKILL_GUIDE.md)
```

---

## Setup Greenfield

Para projetos novos ou sem nenhum setup de workflow existente.

### Se o usuário tem OpenClaude instalado

Peça para ele executar dentro do projeto:

```bash
opencode
# depois, dentro do OpenClaude:
/init-workflow
```

A skill vai:
1. Descobrir o stack do projeto
2. Gerar `CLAUDE.md`, `AGENTS.md`, `.claude/agents/*.md`, `settings.json`, `memory/MEMORY.md`
3. Mostrar o que foi detectado e pedir confirmação
4. Explicar como usar o workflow

### Se o usuário não tem OpenClaude (setup manual)

1. Copie `template/` para a raiz do projeto dele
2. Leia `docs/SETUP.md` para configurar OpenClaude + OpenCode Go
3. Instrua o usuário a instalar as dependências primeiro

**Importante:** Edite os placeholders (`{{BACKEND_LANG}}`, `{{FRONTEND_FRAMEWORK}}`, etc.) no `CLAUDE.md`, `AGENTS.md` e agentes com os valores corretos do projeto dele. Detecte o stack real lendo `package.json`, `composer.json`, `go.mod`, etc.

---

## Setup Adoption

Para projetos que já têm código, commits, convenções, e talvez um setup de workflow parcial.

### Se o usuário tem OpenClaude instalado

Peça para ele executar dentro do projeto:

```bash
opencode
# depois, dentro do OpenClaude:
/init-workflow --adopt
```

Se ele quiser ver o que vai acontecer antes:

```bash
/init-workflow --adopt --dry-run
```

A skill vai:
1. **Inventário** — escanear workflow existente, configs adjacentes (Cursor, Copilot), git history
2. **Memory Bootstrapping** — extrair convenções de código, padrões de arquitetura, integrações
3. **Diff Plan** — mostrar o que será criado/sobrescrito/preservado
4. **Merge & Write** — merge inteligente preservando conteúdo customizado
5. **Adoption Report** — sumário do que mudou

### Se o usuário não tem OpenClaude (setup manual)

Você mesmo pode fazer o trabalho de adoção:

1. **Inventário manual:**
   - Leia o `CLAUDE.md` existente (se houver) e extraia regras
   - Leia agentes customizados em `.claude/agents/` (se houver)
   - Analise `git log --oneline -50` para estilo de commit
   - Detecte padrões de branch com `git branch -l`
   - Identifique hotspots com `git log --name-only --pretty=format: | sort | uniq -c | head -20`

2. **Memory Bootstrapping manual:**
   - Leia uma amostra de arquivos de código (5-10 arquivos representativos)
   - Detecte: naming conventions, import patterns, error handling, test patterns
   - Detecte: arquitetura (MVC, Repository, Service Layer), ORM, auth
   - Crie memórias estruturadas no formato do template

3. **Merge:**
   - Copie `template/CLAUDE.md` e faça merge com as regras existentes do projeto
   - Copie `template/AGENTS.md` e preserve agentes custom, adicione os faltantes
   - Para cada agente em `template/.claude/agents/`, verifique se já existe versão customizada
   - `settings.json`: não sobrescreva se já existir configurado
   - Memórias: adicione as bootstrapped sem remover as existentes

4. **Validação pós-merge:**
   - Confirme que comandos (lint, test, build) estão corretos para o projeto
   - Confirme que os paths de diretórios estão certos
   - Verifique se agentes custom foram preservados

---

## Pós-Setup (para ambos os modos)

4. **Pós-validação** — rode `/init-workflow --health` para confirmar que está tudo consistente
5. Sugira revisar os arquivos gerados/mergeados
6. Se aplicável, sugira customizar agentes para o domínio do projeto
7. Sugira testar o workflow em uma tarefa pequena primeiro (veja `template/docs/runbooks/adding-a-feature.md`)
8. Mostre como usar o sistema de memória (`user`, `feedback`, `project`, `reference`)

---

## Se você é uma IA e o usuário pediu "entenda este repo"

Leia, nesta ordem:

1. **`README.md`** — visão geral do projeto
2. **Este arquivo** — fluxo de decisão para aplicar em projetos
3. **`docs/SKILL_GUIDE.md`** — referência completa da skill
4. **`skills/init-workflow/SKILL.md`** — implementação detalhada da skill
5. **`template/AGENTS.md`** — orquestração dos 10 agentes
6. **`template/docs/runbooks/`** — runbooks de uso diário (adicionar feature, corrigir bug, revisar segurança)
7. **`docs/CUSTOMIZATION.md`** — como adaptar para qualquer stack
8. **`template/.claude/agents/*.md`** (pelo menos 2-3 agentes) — formato dos agentes
