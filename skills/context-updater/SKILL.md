# WORKFLOW: ATUALIZAÇÃO INCREMENTAL DO CONTEXTO DE CLIENTES

> Prompt auxiliar B — executar TODA QUARTA ANTES do Prompt C (Relatório Reportei). Lê apenas deltas (novidades desde a última atualização).

---

## OBJETIVO

Atualizar incrementalmente os docs `Contexto - {Cliente}` na pasta "Contexto Clientes - Stark" com novidades desde a última atualização. Não reler histórico antigo. Roda obrigatoriamente antes do Prompt C.

---

## CONFIGURAÇÕES FIXAS

- **Pasta consolidada:** "Contexto Clientes - Stark" no Drive.
- **Subpasta de documentos do cliente:** buscar por substring case-insensitive contendo "documento".
- **Remetente das transcrições:** `gemini-notes@google.com`
- **Cliente excluído permanentemente:** Dr. Fernando Mattioli - FACE.

---

## PASSOS DE EXECUÇÃO

### PASSO 1 — Listar docs da pasta consolidada

- Buscar todos os docs em "Contexto Clientes - Stark" cujo nome comece com `Contexto - `.
- Resultado esperado: 11 docs (1 por cliente ativo).
- Se algum cliente da lista não tiver doc → registrar e sugerir rodar o Prompt A para esse cliente.

### PASSO 2 — Ler "Última Atualização" de cada doc (paralelo)

- Para cada doc, ler conteúdo e extrair valor após `**Última Atualização:**` (formato `YYYY-MM-DD`).
- Persistir em memória local: `{ cliente, doc_id, ultima_atualizacao }`.

### PASSO 3 — Buscar deltas (paralelo, 22 chamadas)

**Gmail por cliente:**

```
from:gemini-notes@google.com subject:"Anotações" subject:"{Nome Sobrenome}" after:{ultima_atualizacao}
```

**Drive por cliente:**

- Listar arquivos da subpasta "Documentos" do cliente com `modifiedTime > ultima_atualizacao`.

### PASSO 4 — Ler conteúdo dos novos itens (paralelo, batches)

- Capturar apenas o que é novo desde a última atualização.
- Pular arquivos > 200KB e mídias.

### PASSO 5 — Atualizar docs (paralelo, até 11 chamadas)

Para cada cliente que tem novidades:

- **Histórico de Reuniões:** acrescentar bullets datados em ordem cronológica decrescente (novos no topo da seção).
- **Momento Comercial Atual:** substituir o conteúdo SE houver mudança explícita (ex: nova meta, mudança de status comercial, novo desafio recorrente). Caso contrário, manter como está.
- **Pontos de Atenção Recorrentes:** acrescentar/refinar quando um tema aparecer em ≥ 2 reuniões.
- **Aprendizados de Tráfego:** NÃO MEXER. Essa seção é exclusiva do Prompt C.
- **Última Atualização:** atualizar para `YYYY-MM-DD` de hoje.
- **Modo da última atualização:** alterar para `INCREMENTAL`.

### PASSO 6 — Output final

Reportar para cada cliente:

- ✅ atualizado (X transcrições novas, Y docs novos no Drive)
- ⏸️ sem novidades — doc não foi modificado
- ❌ erro — descrever causa

---

## REGRAS

- **Não duplicar:** antes de inserir um bullet em "Histórico de Reuniões", checar se já existe entrada com a mesma data e título.
- **Idempotência:** rodar 2x no mesmo dia não deve gerar duplicações nem alterar conteúdo válido.
- **Sem mudança = sem write:** se nada mudou, NÃO atualizar o campo "Última Atualização" (mantém o cursor original para próximas execuções).
- **Conservadorismo no "Momento Comercial":** só substituir se houver evidência clara — citação direta do cliente, mudança de meta, novo desafio mencionado. Caso ambíguo, manter.
- **Conteúdo de transcrição:** capturar resumo + decisões + ações — NÃO o transcript bruto.
