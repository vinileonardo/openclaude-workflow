# GitHub CLI (gh) — Patterns Que Funcionam

## Setup do Token

```bash
# SEMPRE limpar CRLF do codex.env
export GH_TOKEN=$(grep GITHUB_TOKEN /home/leo/.config/bookado/codex.env | cut -d= -f2 | tr -d '\r\n')
```

## Issues

### Criar com body longo
```bash
# FAIL: heredoc com quote aninhadas quebra
gh issue create --body "texto com $variavel"  # pode quebrar

# OK: arquivo temporário + $(cat)
cat > /tmp/issue-body.md << 'EOF'
## Título
Body aqui sem preocupação com escaping
EOF
gh issue create --body "$(cat /tmp/issue-body.md)"
```

### Fechar com comentário
```bash
gh issue close <N> --comment "## ✅ Feito\n\nDescrição do que foi feito."
```

### Listar por label/milestone
```bash
gh issue list --label "sprint-3" --state all --json number,title,state,milestone
```

## Labels

### Adicionar labels (--label falha frequentemente)
```bash
# FAIL: gh issue create --label "security,medium"  # pode falhar silenciosamente

# OK: criar sem label, depois adicionar via API
gh api repos/vinileonardo/academia/issues/<N>/labels -X POST -f labels[]=security -f labels[]=medium
```

## Project Board (GitHub Projects v2)

### Descobrir o project ID
```bash
gh api graphql -f query='{repository(owner:"vinileonardo",name:"academia"){projectsV2(first:10){nodes{id title number}}}}'
```

### Descobrir field IDs (Status, Priority, etc.)
```bash
gh api graphql -f query='{node(id:"<PROJECT_ID>"){... on ProjectV2{fields(first:20){nodes{... on ProjectV2SingleSelectField{id name options{id name}}}}}}}'
```

### Adicionar issue ao board
```bash
# OK: via URL
gh project item-add <PROJECT_NUMBER> --owner vinileonardo --url "https://github.com/vinileonardo/academia/issues/<N>"

# OK: via GraphQL (quando URL falha)
NODE_ID=$(gh api graphql -f query='{repository(owner:"vinileonardo",name:"academia"){issue(number:<N>){id}}}' --jq '.data.repository.issue.id')
gh api graphql -f query="mutation { addProjectV2ItemById(input: {projectId: \"<PROJECT_ID>\", contentId: \"$NODE_ID\"}) { item { id } } }"
```

### Mover item para "Done"
```bash
gh project item-list <PROJECT_NUMBER> --owner vinileonardo --limit 200 2>&1 | \
  grep -P "\t<N>\t" | awk -F'\t' '{print $5}'  # pega o ITEM_ID

gh api graphql -f query="mutation { updateProjectV2ItemFieldValue(input: {
  projectId: \"<PROJECT_ID>\",
  itemId: \"<ITEM_ID>\",
  fieldId: \"<STATUS_FIELD_ID>\",
  value: {singleSelectOptionId: \"<DONE_OPTION_ID>\"}
}) { clientMutationId } }"
```

### Listar itens no board
```bash
gh project item-list <PROJECT_NUMBER> --owner vinileonardo --limit 200 2>&1 | \
  grep -P "\t(NNN|MMM)\t"  # filtra por número da issue
```

## Milestones

```bash
# Listar
gh api repos/vinileonardo/academia/milestones --jq '.[].title'

# Fechar
gh api repos/vinileonardo/academia/milestones/<N> -X PATCH -f state=closed
```

## PRs

```bash
# Criar (body via arquivo é mais seguro)
gh pr create --base master --title "feat: title" --body "$(cat /tmp/pr-body.md)"

# Verificar status
gh pr view <N> --json state,mergeable,mergedAt

# Mergear
gh pr merge <N> --merge --subject "commit message"
```

## Erros Comuns

| Sintoma | Causa | Solução |
|---------|-------|---------|
| `HTTP 422` no create issue | Body mal formatado | Usar arquivo temporário |
| `--label` ignorado | Label não existe ou conflito | Criar sem label, adicionar depois via API |
| `jq: parse error` | Resposta não é JSON | Verificar se usou `--jq` no comando certo |
| GraphQL mutation falha | String sem escaping | Verificar aspas duplas dentro da string |
| `item-add` returns OK mas não adiciona | Sintaxe errada | Usar `--url` ou GraphQL direto |
| Token com `\r` no final | CRLF no arquivo de env | `tr -d '\r\n'` |
