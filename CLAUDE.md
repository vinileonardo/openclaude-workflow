# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## ⚠️ LEIA PRIMEIRO — AI_ENTRYPOINT.md

Se você é uma IA e foi direcionada a este repositório para configurar o workflow em um projeto, **leia `AI_ENTRYPOINT.md` antes de qualquer outro arquivo**.

Lá está o fluxo de decisão completo:
- **Setup Greenfield** → projetos novos
- **Setup Adoption** → projetos existentes com código e convenções
- **Setup manual** → sem OpenClaude instalado

---

## Project Overview

**OpenClaude Workflow** é um template de workflow multi-agente para OpenClaude. Ele fornece uma estrutura drop-in com 10 agentes especializados, sistema de memória, e um pipeline de orquestração para desenvolvimento de software assistido por IA.

## Architecture

```
openclaude-workflow/
├── template/                    # Cópia para projetos alvo
│   ├── CLAUDE.md                # Pointer com placeholders
│   ├── AGENTS.md                # Orquestração completa
│   ├── .claude/
│   │   ├── settings.json        # Config OpenClaude
│   │   └── agents/              # 10 definições de agentes (.md)
│   └── memory/
│       ├── MEMORY.md            # Índice de memória
│       └── _examples/           # Exemplos de memórias
├── skills/
│   └── init-workflow/           # Skill de setup (/init-workflow)
│       ├── SKILL.md             # Instruções da skill
│       └── references/          # Padrões de detecção
├── docs/
│   ├── SETUP.md                 # Guia de setup
│   ├── CUSTOMIZATION.md         # Guia de customização
│   └── AGENT_TIERS.md           # Filosofia de modelos
└── archive/                     # Referências legadas
```

### Template System

O diretório `template/` é copiado para projetos alvo. Todos os arquivos contêm placeholders (`{{PLACEHOLDER}}`) que o `/init-workflow` substitui com valores detectados do projeto alvo.

### 10 Agentes e Pipeline de Orquestração

```
spec-driver → product-owner → explorer → devs → test-writer → code-reviewer
                                                             + doc-writer (paralelo)
                                                                  ↓
                                                         security-reviewer
                                                                  ↓
                                                               devops
```

### Model Tiers

| Modelo | Uso | Agents |
|---|---|---|
| `deepseek-v4-flash` | Rápido/barato, read-only | Explorer, Doc Writer |
| `qwen3.7-plus` | Workhorse, implementação | Backend/Frontend/Test/DevOps |
| `deepseek-v4-pro` | Análise profunda, revisão | Spec Driver, PO, Code/Security Review |

### Agent Definitions

Cada agente em `template/.claude/agents/*.md` tem:
- **Frontmatter**: name, description, tools, model, permissionMode, color
- **Body**: instruções específicas do papel, checklists, formato de entrega
- Placeholders (`{{BACKEND_LANG}}`, `{{FRONTEND_FRAMEWORK}}`, etc.) preenchidos pelo skill

### Sistema de Memória

Memórias persistentes entre sessões, tipadas em 4 categorias:
- `user` — perfil, preferências
- `feedback` — correções e abordagens confirmadas
- `project` — contexto, decisões ativas
- `reference` — recursos externos

## Key Files

- `template/AGENTS.md` — Pipeline de orquestração e taxonomia de agentes
- `template/CLAUDE.md` — Template de CLAUDE.md para projetos alvo
- `template/.claude/settings.json` — Configuração OpenClaude
- `template/.claude/agents/*.md` — Definições individuais dos 10 agentes
- `skills/init-workflow/SKILL.md` — Implementação do `/init-workflow`
- `skills/init-workflow/references/discovery-patterns.md` — Padrões para detectar stack de projetos
- `skills/init-workflow/references/stack-patterns.md` — Mapeamento stack → placeholders
- `docs/AGENT_TIERS.md` — Filosofia de 3-model tiers
- `docs/CUSTOMIZATION.md` — Como adicionar/modificar agentes

## Critical Commands

```bash
# Usar o setup skill (dentro do OpenClaude em um projeto alvo)
/init-workflow                 # Setup greenfield (projetos novos)
/init-workflow --check         # Apenas descobrir, não gerar
/init-workflow --update        # Regenerar do template existente
/init-workflow --adopt         # Adotar projeto existente (merge + bootstrap)
/init-workflow --adopt --dry-run  # Preview da adoção sem escrever nada
/init-workflow --health        # Diagnóstico: verificar consistência do setup
/init-workflow --prune         # Limpeza: remover arquivos órfãos e temporários
/init-workflow --prune --dry-run  # Preview da limpeza sem deletar
```

Não há build, test, ou lint para este repositório — ele é puramente markdown template.

## Conventions

- Placeholders seguem o padrão `{{UPPER_SNAKE_CASE}}`
- Frontmatter de agentes é YAML entre `---`
- Modelos seguem nomenclatura OpenCode Go: `deepseek-v4-pro`, `qwen3.7-plus`, `deepseek-v4-flash`
- Permission modes: `plan` (read-only) ou `acceptEdits` (pode escrever)
- O skill `init-workflow` NUNCA modifica o projeto na Phase 1 (Discovery) — só lê
- `archive/` contém referências históricas, não código ativo
