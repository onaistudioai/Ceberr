---
name: ceber
description: Cérebro persistente do usuário no vault Obsidian. SEMPRE consultar no início de cada conversa para carregar contexto de projetos anteriores. SEMPRE salvar ao final de cada conversa (ou quando o usuário pedir "salva no ceber", "registra isso", "anota aí", "ceber", "obsidian"). Cada projeto tem sua própria pasta com CEREBRO.md (índice vivo) e Log-YYYY-MM-DD.md (sessões). É a fonte principal de memória entre conversas.
---

# Ceber — Memória Persistente do Usuário

Skill obrigatória para gerenciar o vault Obsidian do usuário como **fonte primária de memória entre conversas**. Não é opcional: deve ser consultada no início e atualizada ao final de cada sessão de trabalho.

> **Antes de instalar:** substitua `<VAULT_PATH>` (em todo este arquivo) pelo caminho absoluto do seu vault Obsidian — por exemplo `~/obsidian/vault` no Linux/macOS ou `C:\Users\voce\obsidian\vault` no Windows. Ajuste também a tabela de mapeamento de projetos na seção "Identificação de Projeto".

---

## Vault

Caminho fixo: `<VAULT_PATH>`

**Estrutura em 3 fluxos:**

```
<VAULT_PATH>/
│
├── 📘 FLUXO 1 — CÉREBRO DO USUÁRIO (só LEITURA pela IA)
│   ├── <cerebro-pessoal>/          ← cérebro pessoal do usuário
│   ├── <Cerebro-Central>/          ← índice mestre do usuário
│   ├── <NomeProjeto>/              ← decisões, propostas, planejamento manual
│   │   ├── CEREBRO.md              ← índice vivo do projeto (USER edita)
│   │   ├── <documento-original>.md ← documentos originais do user
│   │   └── ... (outros arquivos do user)
│   └── ...
│
├── 🤖 FLUXO 2 — PASTA DA IA (única onde a IA escreve livremente)
│   └── _IA/
│       ├── INDEX.md                ← índice do que tem aqui
│       ├── logs/
│       │   └── <NomeProjeto>/
│       │       └── Log-YYYY-MM-DD.md  ← log de sessão (IA escreve)
│       ├── relatorios/
│       │   └── <NomeProjeto>/
│       │       └── *.md            ← análises e relatórios
│       ├── outputs/                ← drafts, rascunhos
│       └── skills-state/           ← estado persistente de skills
│
└── 🔗 FLUXO 3 — INTEGRAÇÃO
    A IA gera no FLUXO 2 consultando o FLUXO 1 como contexto.
    Cada log/relatório linka [[Projeto/arquivo-do-user]] que foi consultado.
```

**Regra inquebrável:** a IA SOMENTE escreve dentro de `_IA/`. Tudo fora é leitura.

Se o usuário pedir "atualiza o CEREBRO.md do projeto X" (que está no Fluxo 1), a IA deve:
1. Avisar que CEREBRO.md é Fluxo 1 (cérebro do user)
2. Propor: editar essa nota ou gerar resumo equivalente em `_IA/relatorios/X/CEREBRO-IA-YYYY-MM-DD.md`
3. Aguardar autorização explícita pra escrever no Fluxo 1

---

## Regra Inquebrável #1 — Início de Cada Conversa

**SEMPRE no início de uma nova conversa**, antes de responder qualquer coisa substancial:

1. Liste `<VAULT_PATH>/` para ver os projetos existentes
2. Leia `<VAULT_PATH>/INDEX.md` se existir
3. Se o usuário mencionar um projeto identificável (por nome ou por contexto da pasta de trabalho), leia o `CEREBRO.md` desse projeto + o último `Log-*.md`
4. Use essas informações como contexto base — não pergunte coisas que já estão registradas no Ceber

Se o Ceber tem informação conflitante com o que o usuário diz agora, **prevalece o que o usuário diz agora** e você atualiza o Ceber depois.

---

## Regra Inquebrável #2 — Fim de Cada Conversa (ou Sessão de Projeto)

**SEMPRE salve no Ceber quando:**

- O usuário pedir explicitamente ("salva no ceber", "registra", "anota", "obsidian")
- A sessão atingir uma conclusão natural (feature entregue, deploy feito, decisão fechada)
- O usuário sinalizar fim ("fechamos por hoje", "obrigado", "tchau", "até amanhã")
- Você terminar uma sequência longa de trabalho num projeto e for mudar de assunto

**O que salvar:**

- Decisões técnicas tomadas (e o porquê)
- Arquivos/pastas criados ou modificados (com caminhos absolutos)
- Comandos importantes executados
- Credenciais/IDs/URLs gerados (referência, nunca o valor secreto)
- Problemas encontrados + soluções (reutilizáveis)
- Próximos passos pendentes

**O que NÃO salvar:**

- Conversa banal / small talk
- Diffs de código completos (resuma a intenção)
- Tokens/secrets em texto puro (anote que foi gerado, não o valor)

---

## Identificação de Projeto

Para identificar o projeto da conversa atual:

1. **Pasta de trabalho atual** (cwd) — se for `<caminho>/X/`, o projeto é `X`
2. **Menção explícita** do usuário ("o projeto Y", "no portfólio")
3. **Pista pelo conteúdo** (repo GitHub, domínio de deploy, nome de arquivo dominante)
4. **Se ambíguo** — pergunte antes de salvar em pasta errada

Mapeamento conhecido (atualize conforme descobre novos):

| Projeto | Pasta no Ceber | cwd típico |
|---------|----------------|------------|
| `<Nome do Projeto A>` | `<VAULT_PATH>/<Pasta-A>` | `<caminho/do/projeto-a>` |
| `<Nome do Projeto B>` | `<VAULT_PATH>/<Pasta-B>` | `<caminho/do/projeto-b>` |

Convenção de nome: **PascalCase com hífen** (ex: `Meu-Projeto-Novo`).

---

## Templates

### `Log-YYYY-MM-DD.md`

```markdown
# Log — YYYY-MM-DD | <Nome do Projeto>

## O que foi feito

- [Lista concreta de ações: 3–10 bullets, nada vago]

## Arquivos e comandos

- `<caminho/do/arquivo>` — o que mudou
- Comando: `npm install ...` — para quê

## Decisões / Aprendizados

- [Decisões técnicas, padrões adotados, descobertas reutilizáveis]

## Problemas + Soluções

- **Problema:** [...]
  **Solução:** [...]

## Credenciais / Identificadores gerados

- [URL, ID, domínio — nunca tokens em texto puro]

## Próximos passos

1. [...]
2. [...]

## Conexões

- [[CEREBRO]]
- [[Log-data-anterior]]
- [[OutroProjeto/CEREBRO]] (se houver cruzamento)

---
*Registrado em YYYY-MM-DD às HH:MM*
```

### `CEREBRO.md` (criar na primeira sessão, atualizar nas seguintes)

```markdown
# Cérebro — <Nome do Projeto>

> Índice vivo. Sempre que mudar, atualize "Estado atual" e "Última atualização".

## O que é

[2–4 linhas: o que é, objetivo, stack principal]

## Estado atual

[1 frase descrevendo onde está agora]

**Última atualização:** YYYY-MM-DD

## Stack / Tech

- [Tecnologias principais]

## URLs / Endpoints

| O que | URL |
|-------|-----|
| Repo | https://... |
| Deploy | https://... |

## Caminhos importantes

| Caminho | Descrição |
|---------|-----------|
| `<caminho>` | ... |

## Fases

- [x] Fase concluída
- [ ] Fase em andamento
- [ ] Fase futura

## Decisões arquiteturais

- [Decisões que afetam o projeto inteiro — porquê de cada uma]

## Logs

- [[Log-YYYY-MM-DD]] — resumo de 1 linha
- [[Log-YYYY-MM-DD]] — resumo de 1 linha
```

### `INDEX.md` (raiz do vault)

```markdown
# Ceber — Índice Mestre

> Lista de todos os projetos registrados. Atualize ao criar projeto novo.

## Projetos ativos

- [[<Pasta-A>/CEREBRO]] — descrição curta de 1 linha
- [[<Pasta-B>/CEREBRO]] — descrição curta de 1 linha

## Projetos pausados / arquivados

- [[<Pasta-C>/CEREBRO]] — descrição curta de 1 linha
```

---

## Passo a Passo da Skill

### Ao Carregar Contexto (início da conversa)

1. `ls <VAULT_PATH>/` → ver projetos do FLUXO 1
2. Leia `_IA/INDEX.md` pra ver outputs anteriores da IA
3. Identifique o projeto desta conversa (cwd, menção, conteúdo)
4. **FLUXO 1 (read-only):** Leia `<Projeto>/CEREBRO.md` se existir (cérebro do user sobre o projeto)
5. **FLUXO 2 (read):** Leia o último `_IA/logs/<Projeto>/Log-*.md` pra ver onde a IA parou
6. **Não anuncie** que carregou — apenas use o contexto naturalmente

### Ao Salvar (fim ou sob demanda)

1. Identifique o projeto
2. **FLUXO 2 (escrita livre):**
   - Se a subpasta `_IA/logs/<Projeto>/` não existir, crie
   - Crie `_IA/logs/<Projeto>/Log-YYYY-MM-DD.md` (ou `-HH.md` se já houver um do dia)
   - Atualize `_IA/INDEX.md` com bullet do novo log
3. **FLUXO 1 (NÃO TOCAR sem permissão):**
   - Se o CEREBRO.md do projeto precisa de atualização factual (ex: URL nova, ID novo de recurso), AVISE o user e pergunte se ele quer atualizar ele mesmo OU autorizar a IA a editar
4. Confirme ao usuário:
   ```
   Salvo no Ceber:
   📁 <VAULT_PATH>/_IA/logs/<Projeto>/Log-YYYY-MM-DD.md
   📋 _IA/INDEX.md atualizado
   [resumo 1–2 linhas]
   ⚠️ CEREBRO.md do projeto pode estar desatualizado em: [X, Y, Z]. Quer editar você ou me autorizar?
   ```

---

## Regras de Estilo

- **Idioma consistente** (tanto índice quanto logs)
- **Caminhos absolutos** sempre que mencionar arquivos
- **Nada vago** — "foi discutido X" é proibido; escreva o conteúdo real
- **Conecte com `[[...]]`** sempre que houver relação com outra nota
- **Sem tokens em texto puro** — registre "token gerado para escopo X" e marque pra revogar
- **Mínimo de emojis** — só na mensagem de confirmação final

---

## Anti-Padrões (Não Faça)

- ❌ Salvar conversa banal sem decisões reais
- ❌ Criar projeto novo sem atualizar `INDEX.md`
- ❌ Pedir ao usuário info que já está no `CEREBRO.md`
- ❌ Sobrescrever um `Log-*.md` do mesmo dia (use sufixo `-HH`)
- ❌ Esquecer de atualizar "Última atualização" no `CEREBRO.md`
- ❌ Salvar tokens, senhas ou chaves em texto puro

---

## Instalação

1. Copie este arquivo para `<dir-home>/.claude/skills/ceber/SKILL.md`.
2. Substitua todas as ocorrências de `<VAULT_PATH>` pelo caminho real do seu vault Obsidian.
3. Preencha a tabela de "Identificação de Projeto" com seus projetos.
4. Crie a estrutura inicial no vault:
   ```
   <VAULT_PATH>/_IA/logs/
   <VAULT_PATH>/_IA/relatorios/
   <VAULT_PATH>/_IA/outputs/
   <VAULT_PATH>/_IA/skills-state/
   <VAULT_PATH>/_IA/INDEX.md
   <VAULT_PATH>/INDEX.md
   ```
5. (Opcional) Adicione os triggers no `CLAUDE.md` do(s) seu(s) projeto(s):
   ```markdown
   ## ceber
   Skill ativo. Vault: <VAULT_PATH>
   Sempre consultar no início de cada conversa. Sempre salvar ao final.
   Triggers: "salva no ceber", "registra isso", "anota aí", "ceber", "obsidian".
   ```
