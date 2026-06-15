# Ceberr — Sistema de Memória Persistente para Claude Code

> Cérebro persistente entre sessões do Claude Code via vault Obsidian. Economia de 60–90% de tokens ao eliminar re-contextualizações repetidas.

---

## O que é o Ceber

O Ceber é um **skill de memória persistente** para o Claude Code. Ele resolve o problema fundamental dos LLMs: cada conversa começa do zero. Com o Ceber, o histórico de decisões, arquitetura, credenciais geradas e próximos passos ficam salvos no Obsidian e são carregados automaticamente em cada nova sessão.

**Sem ceber:** você reexplica o projeto toda vez → gasta tokens → o Claude toma decisões sem contexto histórico.

**Com ceber:** Claude carrega o estado atual em segundos → continua de onde parou → decisões são coerentes com o histórico.

---

## Arquitetura — Os 3 Fluxos

```
FLUXO 1: Cérebro do Usuário (IA só lê)
├── <Projeto>/CEREBRO.md          ← índice vivo do projeto
├── <Projeto>/*.md                ← docs, decisões, propostas
└── <Cerebro-Central>/            ← índice mestre

FLUXO 2: Pasta da IA (IA só escreve aqui)
└── _IA/
    ├── INDEX.md                  ← índice de tudo que a IA gerou
    ├── logs/<Projeto>/Log-YYYY-MM-DD.md
    ├── relatorios/<Projeto>/*.md
    ├── outputs/                  ← rascunhos e gerados
    └── skills-state/             ← estado persistente

FLUXO 3: Integração
IA consulta Fluxo 1 como contexto → gera em Fluxo 2 → linka ambos.
```

**Regra inquebrável:** A IA NUNCA edita fora de `_IA/` sem permissão explícita do usuário.

---

## Parâmetros de Funcionamento

### Variáveis de Configuração

| Parâmetro | Exemplo | Descrição |
|-----------|---------|-----------|
| `vault_path` | `<caminho-do-seu-vault>` | Caminho raiz do vault Obsidian |
| `fluxo_1_path` | `<Projeto>/` | Leitura — cérebro do usuário |
| `fluxo_2_path` | `_IA/logs/<Projeto>/` | Escrita — logs da IA |
| `project_name` | Auto-detectado | Inferido do cwd ou menção do usuário |
| `log_date` | `YYYY-MM-DD` | Formato ISO; usar `-HH` se mesmo dia |
| `language` | configurável | Idioma dos índices e logs |

### Mapeamento Projeto → Pasta Ceber

Cada projeto no seu ambiente é mapeado para uma pasta no vault. Defina esse mapeamento no `SKILL.md`:

| Projeto | Pasta Ceber | cwd típico |
|---------|-------------|------------|
| `<Nome do Projeto A>` | `<Pasta-A>` | `<caminho/do/projeto-a>` |
| `<Nome do Projeto B>` | `<Pasta-B>` | `<caminho/do/projeto-b>` |

**Convenção de nomes:** PascalCase com hífens → `Meu-Projeto-Novo`

---

## Fluxo Obrigatório — Início de Sessão

```
1. Identificar projeto (cwd ou menção do usuário)
2. Ler <Projeto>/CEREBRO.md          ← estado atual do projeto
3. Ler _IA/logs/<Projeto>/Log-mais-recente.md  ← onde a IA parou
4. Usar como base de contexto — NÃO anunciar que carregou
5. Se conflito entre Ceber e o que usuário diz agora → usuário prevalece
```

---

## Fluxo Obrigatório — Fim de Sessão

Salvar quando:
- Usuário pede explicitamente: `"salva no ceber"`, `"registra isso"`, `"anota aí"`, `"obsidian"`
- Sessão chega a conclusão natural (feature entregue, deploy feito, decisão fechada)
- Usuário sinaliza fim: `"fechamos"`, `"obrigado"`, `"tchau"`

```
1. Identificar projeto
2. Criar/atualizar _IA/logs/<Projeto>/Log-YYYY-MM-DD.md
3. Atualizar _IA/INDEX.md com nova entrada + resumo 1 linha
4. Avisar se CEREBRO.md precisa atualização, pedir permissão para editar
5. Confirmar para o usuário:
   📁 <vault_path>/_IA/logs/<Projeto>/Log-YYYY-MM-DD.md
   📋 _IA/INDEX.md atualizado
   [resumo 1–2 linhas]
```

---

## Estrutura dos Arquivos

### `CEREBRO.md` — Índice Vivo do Projeto (Usuário edita)

```markdown
# Cérebro — <Nome do Projeto>

> Índice vivo. Sempre que mudar, atualize "Estado atual" e "Última atualização".

## O que é
[2–4 linhas: objetivo, stack principal]

## Estado atual
[1 frase onde está agora]
**Última atualização:** YYYY-MM-DD

## Stack / Tech
- [Tecnologias principais]

## URLs / Endpoints
| O que | URL |
|-------|-----|
| Repo  | https://... |
| Deploy | https://... |

## Caminhos importantes
| Caminho | Descrição |
|---------|-----------|
| `<caminho>` | ... |

## Fases
- [x] Fase concluída
- [ ] Fase em andamento

## Decisões arquiteturais
- [Decisões que afetam o projeto inteiro — porquê de cada uma]

## Logs
- [[Log-YYYY-MM-DD]] — resumo de 1 linha
```

---

### `Log-YYYY-MM-DD.md` — Log de Sessão (IA escreve)

```markdown
# Log — YYYY-MM-DD | <Nome do Projeto>

## Contexto da sessão
Consultados (Fluxo 1): [[CEREBRO]], [arquivo], ...

## O que foi feito
- [3–10 bullets concretos, nada vago]

## Arquivos e comandos
- `<caminho/do/arquivo>` — o que mudou
- Comando: `npm install ...` — para quê

## Decisões / Aprendizados
- [Decisões técnicas, padrões, descobertas reutilizáveis]

## Problemas + Soluções
- **Problema:** [...]
  **Solução:** [...]

## Credenciais / Identificadores gerados
- [URL, ID, domain — NUNCA tokens em texto puro]

## Próximos passos
1. [...]
2. [...]

## Conexões
- [[CEREBRO]]
- [[Log-data-anterior]]

---
*Registrado em YYYY-MM-DD às HH:MM*
```

---

### `_IA/INDEX.md` — Índice Mestre de Outputs da IA

Navegação centralizada de tudo que a IA gerou, organizado por projeto. Cada entrada tem:
- Data do log
- Título descritivo (ex: `"Gate 0.1 ✅"`, `"Staging resolvido"`, `"Deploy concluído"`)
- Resumo de 1 linha do que foi feito

---

## Regras de Escrita

```
✅ Idioma consistente em índices e logs
✅ Caminhos absolutos sempre (nunca relativos)
✅ NUNCA vago — "foi discutido X" é proibido; escreva o conteúdo real
✅ Linkar com [[...]] wiki links quando relacionado a outras notas
✅ NUNCA tokens/senhas em texto puro — escreva "token gerado para escopo X"
✅ Emojis mínimos — só na confirmação final
```

---

## Anti-Padrões (Proibido)

```
❌ Salvar conversa banal sem decisões reais
❌ Criar projeto sem atualizar INDEX.md
❌ Re-perguntar o que já está no CEREBRO.md
❌ Sobrescrever um Log-*.md do mesmo dia (usar sufixo -HH)
❌ Esquecer de atualizar "Última atualização" no CEREBRO.md
❌ Salvar tokens/senhas/chaves em texto puro
❌ Tocar Fluxo 1 sem permissão explícita
❌ Misturar idiomas em um mesmo log
```

---

## Integração com Claude Code

O Ceber é instalado como um **skill** do Claude Code. O skill fica em:
```
<dir-home>/.claude/skills/ceber/SKILL.md
```

### Configuração no `CLAUDE.md` do projeto

```markdown
## ceber
Skill ativo. Vault: <caminho-do-seu-vault>
Sempre consultar no início de cada conversa.
Sempre salvar ao final de cada conversa.
```

### Triggers de ativação

| Trigger | Ação |
|---------|------|
| `/ceber` | Invoca o skill explicitamente |
| `"salva no ceber"` | Dispara salvamento de sessão |
| `"registra isso"` | Dispara salvamento de sessão |
| `"anota aí"` | Dispara salvamento de sessão |
| `"obsidian"` | Dispara salvamento de sessão |

---

## Integração com Graphify (opcional)

O Ceber pode ser consultado via **graphify** para busca semântica no grafo de conhecimento:

```bash
# Buscar contexto relevante antes de invocar o ceber
graphify query "<topico>" --graph "<vault_path>/graphify-out/graph.json" --budget 1500

# Buscar relacionamento entre conceitos
graphify path "<A>" "<B>" --graph "..."

# Explicar um conceito específico
graphify explain "<conceito>" --graph "..."
```

**Regra:** Executar `graphify query` ANTES de invocar o skill ceber quando há planejamento, projeção ou início de algo novo. O graphify é o atendente que sabe onde tudo está; o ceber é o cofre.

---

## Economia de Tokens

O principal benefício operacional do ceber é a **eliminação de re-contextualizações**:

| Sem Ceber | Com Ceber |
|-----------|-----------|
| Reexplica projeto toda sessão | Carrega contexto em segundos |
| Claude toma decisões sem histórico | Decisões coerentes com histórico |
| Decisões contraditórias entre sessões | Consistência entre sessões |
| ~2.000–5.000 tokens de contexto perdido | ~200–500 tokens para carregar estado |

**Economia estimada:** 60–90% em operações de desenvolvimento contínuo.

---

## Como instalar

1. Crie o vault Obsidian (ou use um existente) e anote o caminho raiz.
2. Crie a pasta `_IA/` na raiz do vault com as subpastas `logs/`, `relatorios/`, `outputs/`, `skills-state/`.
3. Copie o skill para `<dir-home>/.claude/skills/ceber/SKILL.md` e ajuste `vault_path` e o mapeamento de projetos.
4. Adicione os triggers no `CLAUDE.md` do(s) seu(s) projeto(s).
5. Para cada projeto, crie um `<Projeto>/CEREBRO.md` a partir do template acima.
