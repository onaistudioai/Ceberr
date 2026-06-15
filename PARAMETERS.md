# Parâmetros do Ceber — Referência Técnica

Referência completa de todos os parâmetros, convenções e variáveis de controle do sistema Ceber.

---

## Variáveis de Ambiente / Configuração

### `vault_path`
- **Valor:** caminho absoluto do vault Obsidian (definido no `SKILL.md`)
- **Tipo:** caminho absoluto (string)
- **Mutabilidade:** fixo por instalação — altere no `SKILL.md` se mudar o vault
- **Uso:** raiz de todos os caminhos do Ceber

### `project_name`
- **Tipo:** string (PascalCase com hífens)
- **Auto-detecção:** inferido do cwd → `<caminho/do/projeto>` → `<Pasta-Ceber>`
- **Override:** usuário menciona explicitamente o nome do projeto
- **Exemplo de formato:** `Meu-Projeto`, `Outro-Projeto`

### `log_date`
- **Formato:** `YYYY-MM-DD` (ISO 8601)
- **Sufixo de colisão:** `-HH` quando há múltiplas sessões no mesmo dia
- **Exemplos:**
  - `Log-2026-01-15.md` — sessão única no dia
  - `Log-2026-01-15-09.md` e `Log-2026-01-15-15.md` — duas sessões
- **Regra:** NUNCA sobrescrever log existente; sempre criar novo com sufixo `-HH`

### `language`
- **Valor:** configurável (defina no `SKILL.md`)
- **Escopo:** índices, logs, relatórios, confirmações
- **Recomendação:** manter um único idioma consistente por instalação

---

## Parâmetros de Caminho

### Caminhos de Leitura (Fluxo 1 — usuário)

```
{vault_path}/{project_name}/CEREBRO.md
{vault_path}/{project_name}/*.md
{vault_path}/INDEX.md
```

### Caminhos de Escrita (Fluxo 2 — IA)

```
{vault_path}/_IA/INDEX.md
{vault_path}/_IA/logs/{project_name}/Log-{log_date}.md
{vault_path}/_IA/relatorios/{project_name}/*.md
{vault_path}/_IA/outputs/
{vault_path}/_IA/skills-state/
```

---

## Parâmetros de Conteúdo do Log

Cada log deve conter obrigatoriamente:

| Campo | Obrigatório | Descrição |
|-------|-------------|-----------|
| `## Contexto da sessão` | ✅ | Quais arquivos do Fluxo 1 foram consultados |
| `## O que foi feito` | ✅ | 3–10 bullets concretos (sem vagueza) |
| `## Arquivos e comandos` | ✅ se houve mudanças | Caminhos absolutos + o que mudou |
| `## Decisões / Aprendizados` | ✅ se houve decisões | Técnicas e reutilizáveis |
| `## Problemas + Soluções` | ✅ se houve problemas | Formato: Problema / Solução |
| `## Credenciais / Identificadores` | ✅ se foram gerados | NUNCA o valor — só a descrição |
| `## Próximos passos` | ✅ | Lista numerada |
| `## Conexões` | ✅ | Wiki links `[[...]]` |

---

## Parâmetros de Conteúdo do CEREBRO.md

| Campo | Quem edita | Frequência de atualização |
|-------|------------|--------------------------|
| `## O que é` | Usuário | Raro (só se o projeto mudar de escopo) |
| `## Estado atual` | Usuário (+ IA com permissão) | A cada sessão significativa |
| `**Última atualização:**` | Usuário (+ IA com permissão) | Sempre que alterar o CEREBRO |
| `## Stack / Tech` | Usuário | Quando a stack mudar |
| `## URLs / Endpoints` | Usuário (+ IA com permissão) | Quando novos deploys/repos |
| `## Caminhos importantes` | Usuário | Quando criar pastas críticas |
| `## Fases` | Usuário | A cada fase concluída |
| `## Decisões arquiteturais` | Usuário | A cada decisão arquitetural |
| `## Logs` | Usuário | Referência ao último log |

---

## Parâmetros de INDEX.md (`_IA/INDEX.md`)

Estrutura de cada entrada no índice:

```markdown
### <project_name>

| Log | Título | O que foi feito |
|-----|--------|-----------------|
| [[Log-YYYY-MM-DD]] | Título descritivo | Resumo 1 linha |
| [[Log-YYYY-MM-DD-HH]] | Título descritivo | Resumo 1 linha |
```

**Parâmetros de cada linha:**
- `Log-YYYY-MM-DD` — link wiki para o arquivo de log
- `Título descritivo` — ex: `"Gate 0.1 ✅"`, `"Staging resolvido"`, `"Deploy concluído"`
- `Resumo 1 linha` — denso, sem "foi feito X" — direto ao ponto

---

## Parâmetros de Resolução de Conflito

Quando o Ceber contradiz o que o usuário diz na sessão atual:

| Situação | Comportamento |
|----------|---------------|
| Ceber diz X, usuário diz Y agora | Y prevalece. Atualizar Ceber ao final. |
| Ceber tem data/estado desatualizado | Confiar no usuário, marcar para atualização |
| Ceber tem credencial, usuário diz foi revogada | Atualizar Ceber com nota de revogação |
| Ceber tem decisão, usuário quer reverter | Usuário prevalece, registrar reversão no log |

---

## Parâmetros de Segurança

### O que NUNCA salvar em texto puro

```
❌ Tokens de API
❌ Senhas e credenciais de banco de dados
❌ Chaves privadas SSH/GPG
❌ Client secrets OAuth
❌ Variáveis de ambiente com valores sensíveis
```

### Como registrar credenciais geradas

```markdown
## Credenciais / Identificadores gerados
- Token de deploy gerado para o serviço X — escopo: deploy only.
  Armazenado em variável de ambiente `DEPLOY_TOKEN`. Revogar se comprometido.
- Webhook URL gerado e testado.
  Endpoint: configurado em variável `WEBHOOK_URL`.
```

---

## Parâmetros de Integração Claude Code

### Localização do Skill

```
<dir-home>/.claude/skills/ceber/SKILL.md
```

### Triggers no CLAUDE.md

```markdown
## ceber
- ceber = armazenamento de memória no vault Obsidian em <vault_path>
- SEMPRE consultar no início de cada conversa
- SEMPRE salvar ao final de cada conversa
- Triggers: "salva no ceber", "registra isso", "anota aí", "ceber", "obsidian"
```

### Prioridade de detecção de projeto

```
1. Usuário menciona nome explicitamente → usa esse nome
2. cwd match na tabela de mapeamento → usa projeto mapeado
3. Nenhum dos dois → perguntar antes de salvar
```

---

## Parâmetros de Graphify (integração opcional)

Quando usar graphify antes do ceber:

| Situação | Ação |
|----------|------|
| Planejamento de nova feature | `graphify query "<feature>"` primeiro |
| Início de nova fase | `graphify query "<fase>"` primeiro |
| Início de sessão com projeto desconhecido | `graphify query "<projeto>"` primeiro |
| Busca de decisão arquitetural antiga | `graphify path "<A>" "<B>"` |
| Entender conceito do projeto | `graphify explain "<conceito>"` |

```bash
# Budget padrão para queries do ceber
graphify query "<topico>" \
  --graph "<vault_path>/graphify-out/graph.json" \
  --budget 1500
```

---

*Referência técnica do Ceber — Arquitetura 3 Fluxos.*
