# WORKFLOW: PREENCHIMENTO DE STATUS REPORT — MARÇO E ABRIL/2026 (SEMANAL)

> Este prompt documenta **ações do gestor** (o que foi subido, pausado, ajustado, reestruturado, testado) na conta de cada cliente, cliente por cliente, semana por semana. **Não é relatório de métricas** — é um registro narrativo das decisões de gestão tomadas no período.
> Resultado final: cada subpágina vazia em `Agência [Operacional] › Tráfego Pago › Status Report › Status Report - Gustavo › [Cliente]` preenchida com o status das semanas de março e abril/2026.

---

## ⚙️ VARIÁVEIS DE DATA — EDITE APENAS AQUI

```
MES_1_LABEL           = Março/2026
MES_2_LABEL           = Abril/2026

# Semanas (segunda a domingo) — ajuste se quiser calendário diferente
SEMANAS = [
  { label: "Semana 1 — Mar", inicio: "2026-03-02", fim: "2026-03-08" },
  { label: "Semana 2 — Mar", inicio: "2026-03-09", fim: "2026-03-15" },
  { label: "Semana 3 — Mar", inicio: "2026-03-16", fim: "2026-03-22" },
  { label: "Semana 4 — Mar", inicio: "2026-03-23", fim: "2026-03-29" },
  { label: "Semana 5 — Mar/Abr", inicio: "2026-03-30", fim: "2026-04-05" },
  { label: "Semana 1 — Abr", inicio: "2026-04-06", fim: "2026-04-12" },
  { label: "Semana 2 — Abr", inicio: "2026-04-13", fim: "2026-04-19" },
  { label: "Semana 3 — Abr", inicio: "2026-04-20", fim: "2026-04-26" },
  { label: "Semana 4 — Abr", inicio: "2026-04-27", fim: "2026-04-30" }
]
```

---

## 📋 CONFIGURAÇÕES FIXAS

**Localização das subpáginas a preencher (ClickUp Docs):**
`Agência [Operacional] › Tráfego Pago › Status Report › Status Report - Gustavo › [Subpágina do Cliente]`

> As subpáginas já existem e estão **vazias** — o trabalho é preencher, não criar. Ignorar qualquer outra página de cliente fora desta árvore (as `Status Report - Vinicius/Luiz/Amanda/Rayza` etc. NÃO são minhas).

**Cliente EXCLUÍDO permanentemente:** Dr. Fernando Mattioli - FACE (Reportei project_id: 772702) — não preencher.

**Cliente em situação específica — SÓ GOOGLE ADS:** Dr. Laureano Filho (project_id: 982754) — toda análise e ações só devem considerar Google Ads.

**Clientes ativos para preenchimento (11 clientes), com contexto comercial e subpágina alvo no ClickUp:**

| Cliente | project_id (Reportei) | Contexto comercial | Subpágina alvo |
|---|---|---|---|
| Dr. Marcelo Bezerra | 610559 | Baixo volume de agendamentos — atenção | Dr Marcelo Bezerra |
| Dra. Nicolli | 642925 | Boa conversão; monitorar frequência | Dra Nicolli |
| Graciela Machado | 672682 | Bom momento comercial | Graciela Machado |
| Dr. Fernando Bezerra | 696403 | PRIORIDADE — baixo volume de leads | Dr. Fernando Bezerra |
| Fernanda Encinas | 913731 | Alto volume de conversas | Fernanda Encinas |
| Dr. Laureano Filho | 982754 | Só Google Ads ativo | Dr. Laureano Filho |
| Dra. Érica Marchiori | 1025271 | Bom momento de fechamento | Dra. Érica Marchiori |
| Dr. Diego Alencar | 1064037 | CPL crítico | Dr. Diego Alencar |
| Dra. Mariângela Santiago | 1097223 | Ausência de conteúdo orgânico | Dra. Mariângela Santiago |
| Dr. Higner Forastieri | 1097249 | Baixo agendamento, CPM alto | Dr. Higner Forastieri |
| Dr. Caio Fernandes | 1170455 | Alto alcance; monitorar follows | Dr. Caio Fernandes |

---

## 🔌 FONTES DE DADOS DISPONÍVEIS

> **Importante:** não temos MCP do Meta Ads nem do Google Ads neste momento. A reconstrução das ações é **inferencial** — combinamos o que o Reportei registra (timeline/relatórios gerados, variações bruscas de investimento, mudanças de CPL, frequência, CTR e mix de canais) com o contexto humano extraído das transcrições de reunião no Drive.

1. **MCP Reportei** — fonte primária de sinais quantitativos:
   - `list_projects`, `list_integrations(project_id)`
   - `list_timeline_events(project_id, date_start, date_end)` → marcos já registrados no período
   - `get_project_metrics` e `create_report` + `get_report` para o intervalo da semana
   - Sinais a extrair: variação de investimento (indica escala/corte), salto/queda de CPL (indica troca de criativo ou público), mudança de frequência (indica pausa/renovação), CTR inédito (criativo novo), aparecimento/desaparecimento de canal.

2. **Gmail + Google Drive — anotações de reunião (Gemini)** — fonte primária de contexto qualitativo.

   > As transcrições NÃO são gravadas soltas no Drive como arquivos livres. Elas chegam como **e-mails do remetente "Gemini"**, com o assunto no formato `Anotações: "[Título da Reunião]" em [data]` e o doc do resumo anexado (link para arquivo no Drive). O título da reunião sempre contém o nome do cliente (ex.: `Anotações: "Reunião | Diego Alencar" em 22 de abr. de 2026`, `Anotações: "Reunião semanal - Mariângela" em 22 de abr. de 2026`).

   **Passo A — indexar pelo Gmail (busca primária):**
   - Query base por cliente:
     `from:(gemini OR "Google Meet") subject:Anotações "[Nome ou apelido do Cliente]" after:2026/03/01 before:2026/05/01`
   - Variações de nome a tentar por cliente (o título da reunião raramente traz o nome completo):
     - Dr. Marcelo Bezerra → `"Marcelo Bezerra"`, `"Dr. Marcelo"`, `"Marcelo"`
     - Dra. Nicolli → `"Nicolli"`
     - Graciela Machado → `"Graciela"`
     - Dr. Fernando Bezerra → `"Fernando Bezerra"`, `"Dr. Fernando Bezerra"`
     - Fernanda Encinas → `"Fernanda Encinas"`, `"Fernanda"`
     - Dr. Laureano Filho → `"Laureano"`
     - Dra. Érica Marchiori → `"Érica"`, `"Erica Marchiori"`
     - Dr. Diego Alencar → `"Diego Alencar"`, `"Diego"`
     - Dra. Mariângela Santiago → `"Mariângela"`, `"Mariangela"`
     - Dr. Higner Forastieri → `"Higner"`
     - Dr. Caio Fernandes → `"Caio Fernandes"`, `"Caio"`
   - Também dar uma passada com `subject:Anotações after:2026/03/01 before:2026/05/01` (sem nome) e varrer os assuntos — captura reuniões com nomeação atípica (ex.: `Daily de Tráfego`, `Acompanhamento de Resultados - Bloco B`, `Marketing Concierge`) que podem mencionar múltiplos clientes no corpo.

   **Passo B — abrir o doc anexado e ler:**
   - Cada e-mail do Gemini traz um chip com link para o Google Doc das anotações. Abrir o doc (o arquivo vive no Drive) e extrair.
   - Se o link do doc falhar, buscar no Drive pelo título exato do e-mail (`"Anotações: ...em [data]"` retorna o mesmo arquivo).

   **Passo C — extrair:**
   - Data da reunião (está no assunto)
   - Decisões operacionais combinadas (criativos, públicos, orçamento, pausas)
   - Feedback do cliente sobre leads/volume/qualidade
   - Novas diretrizes de conteúdo ou campanha
   - Guardar um mini-dossier por cliente, ordenado por data.

   **Atenção — reuniões multi-cliente:** `Daily de Tráfego`, `Acompanhamento de Resultados - Bloco [A/B/C]` e `Marketing Concierge` normalmente discutem vários clientes na mesma reunião. Ao processar esses docs, separar os trechos por cliente (procurar pelo nome do cliente no corpo do doc) e distribuir em cada dossier. Não descartar esses docs — eles costumam ter as decisões táticas da semana.

   **Atenção — reuniões internas:** e-mails com `Reunião Interna | [Cliente]` são alinhamentos do time (gestor + estrategista + atendimento), geralmente com o briefing do que vai ser feito ou reportado. Usar como fonte de "porquê" das ações.

3. **ClickUp (opcional, se o gestor disponibilizar)** — tarefas concluídas nas listas relevantes para cruzar e confirmar ações:
   - Lista "Criativos a Subir no ADS" (ID: 901304117561) — confirma criativos subidos.
   - Lista "Otimização Campanhas Meta Ads" (ID: 901311804425) — confirma otimizações.
   - Se disponível, tratar como **fonte de verdade** que sobrescreve as inferências do Reportei.

---

## 🚦 CONTROLE DE RATE LIMIT (REPORTEI)

- Limite: **40 requisições por janela de 9 minutos**
- Ao atingir 38 requisições, use `ScheduleWakeup` com `delaySeconds: 540`
- Paralelizar chamadas por cliente sempre que possível
- Distribuição orientativa para cobrir 11 clientes × 9 semanas:
  - **Janela 1:** `list_projects` + 11× `list_integrations` + 11× `list_timeline_events` (período completo mar+abr) + 11× `create_report` (período completo mar+abr) → ~34 chamadas
  - **Janela 2:** 11× `get_report` + até 22× `get_project_metrics` semanais conforme necessidade
  - **Janelas seguintes:** apenas buscas semanais adicionais se faltar granularidade

> Estratégia eficiente: **um único relatório mar+abr** por cliente e derivar as semanas cortando o intervalo na análise, em vez de 9 relatórios por cliente. Só quebre em semanais se houver dúvida específica.

---

## 📐 PASSOS DE EXECUÇÃO

### PASSO 1 — Descoberta e mapeamento
- `clickup_get_workspace_hierarchy` → localizar o doc "Status Report - Gustavo" e suas subpáginas.
- `clickup_list_document_pages` na página "Status Report - Gustavo" → capturar o `page_id` de cada uma das 11 subpáginas.
- `list_projects` no Reportei → confirmar os 11 `project_id` da tabela.
- Excluir Dr. Fernando Mattioli - FACE.

### PASSO 2 — Listar integrações (11 chamadas paralelas)
- `list_integrations(project_id)` para cada cliente → saber quais canais considerar (meta_ads, google_adwords, google_analytics_4, instagram). Para Dr. Laureano Filho, forçar só google_adwords.

### PASSO 3 — Coletar sinais do Reportei (período completo)
Para cada cliente, em paralelo:
- `create_report(template_id, date_start: 2026-03-02, date_end: 2026-04-30)`
- `list_timeline_events(project_id, date_start: 2026-03-01, date_end: 2026-04-30)` → capturar todos os marcos já registrados (se houver, tratar como verdade).
- `get_project_metrics(project_id, date_start: 2026-03-02, date_end: 2026-04-30)`
- Se a granularidade não revelar a ação da semana específica, **só aí** quebrar em semanais.

### PASSO 4 — Coletar contexto das anotações de reunião (em paralelo com Passo 3)

> Fluxo em 3 etapas — **Gmail primeiro** para indexar, **Drive depois** para ler o doc anexado.

**4.1 — Pescar os e-mails do Gemini (Gmail):**
Para cada cliente, executar a query base e as variações de nome listadas na seção "Fontes de Dados":
- `from:(gemini OR "Google Meet") subject:Anotações "[Cliente]" after:2026/03/01 before:2026/05/01`
- Listar todos os threads retornados e capturar: data da reunião (do assunto), título da reunião, link do doc anexado.

**4.2 — Varrer reuniões multi-cliente (uma vez só, não por cliente):**
- `from:(gemini OR "Google Meet") subject:Anotações after:2026/03/01 before:2026/05/01`
- Filtrar os resultados que batam com `Daily de Tráfego`, `Acompanhamento de Resultados - Bloco A/B/C`, `Marketing Concierge` — estas tratam múltiplos clientes na mesma reunião.
- Abrir cada um desses docs e separar os trechos por cliente (busca pelo nome no corpo), distribuindo em cada dossier.

**4.3 — Abrir os docs e extrair (Drive):**
- Para cada doc identificado em 4.1 e 4.2, abrir o arquivo no Drive e extrair:
  - Data da reunião
  - Decisões operacionais combinadas (criativos, públicos, orçamento, pausas)
  - Feedback do cliente sobre leads/volume/qualidade
  - Novas diretrizes de conteúdo ou campanha
  - Se for `Reunião Interna | [Cliente]`, tratar como briefing interno (o "porquê" das ações da semana seguinte).
- Montar um mini-dossier por cliente, ordenado por data, apontando a fonte (título da reunião + data) em cada decisão extraída.

### PASSO 5 — Cruzar e inferir ações por semana
Para cada cliente, para cada uma das 9 semanas do cronograma:
1. Pegar os sinais do Reportei **daquela semana** (variações de investimento, CPL, CTR, frequência).
2. Pegar as decisões registradas em transcrições **daquela semana ou da semana imediatamente anterior** (ação geralmente ocorre após a reunião).
3. Inferir o que provavelmente foi executado:
   - Aumento brusco de investimento → "escalamos a campanha X"
   - Queda súbita de frequência num conjunto + salto de CTR → "pausamos criativos saturados e subimos renovação"
   - Aparecimento de nova campanha na timeline Reportei → "estruturamos nova campanha de [objetivo]"
   - Conversa na transcrição sobre "testar outro ângulo" + posterior salto de CTR → "testamos o ângulo X pedido pelo cliente, resultado preliminar…"
4. Se não houver nenhum sinal de ação na semana: escrever `Semana de manutenção — acompanhamos a performance sem intervenções estruturais.` em vez de inventar ação.

### PASSO 6 — Compilar o texto por semana (seguindo o formato do template)
Gerar o texto completo da subpágina do cliente no formato definido na seção **"📝 TEMPLATE DA SUBPÁGINA"** abaixo.

### PASSO 7 — Apresentar o draft ao gestor (Gustavo)
Antes de escrever no ClickUp, mostrar cliente por cliente o draft final. O gestor confirma, corrige pontos específicos ou aprova em massa.

### PASSO 8 — Escrever nas subpáginas (ClickUp)
Após aprovação:
- Usar `clickup_update_document_page` para cada `page_id` capturado no Passo 1, substituindo o conteúdo pelo texto aprovado.
- **NÃO criar novas páginas, NÃO criar novas tasks.** As subpáginas já existem.
- Se alguma subpágina não for encontrada, **parar e avisar o gestor** — não improvisar criação.

---

## 📝 TEMPLATE DA SUBPÁGINA (uma por cliente)

> O corpo da subpágina do cliente deve ter este formato, em Markdown. Cada semana é uma seção. Texto corrido, primeira pessoa do plural, tom estratégico, sem tabelas frias.

```markdown
# Status Report — [Nome do Cliente] — Março e Abril/2026

> **Contexto comercial no período:** [1 frase extraída da tabela de contexto]

---

## Semana 1 — Mar (02/03 a 08/03)

**Resumo da semana:**
[1–2 parágrafos explicando o foco do trabalho na semana. Ex.: "Nesta semana, nosso foco principal foi a renovação dos criativos de fundo de funil para reduzir o CPL, após termos identificado saturação nos anúncios que vinham rodando desde fevereiro."]

**O que subimos de novo:**
[Parágrafo em texto corrido detalhando criativos, campanhas ou conjuntos novos. Ex.: "Para alimentar o topo de funil, subimos X novos criativos em formato reels focados em [tema], distribuídos na campanha Y. A escolha do ângulo partiu da reunião do dia [data], em que combinamos priorizar autoridade e educação do paciente."]

**O que ajustamos e pausamos:**
[Parágrafo em texto corrido narrando otimizações. Ex.: "Pausamos o anúncio X porque a frequência ultrapassou 3,2 e o CPL saiu da faixa aceitável. Redistribuímos a verba para o conjunto Y, que vinha entregando conversas a um custo 35% menor."]

**Observações adicionais:**
[Só incluir se houver algo relevante extraído das transcrições — pedido específico do cliente, queixa, ajuste de landing page, sazonalidade. Se não houver nada, remover a seção.]

---

## Semana 2 — Mar (09/03 a 15/03)

[...mesma estrutura...]

---

[...repetir para todas as 9 semanas...]

---

## Resumo consolidado do bimestre

[3–5 linhas amarrando a narrativa de março e abril: qual foi a tese de otimização, o que funcionou, o que foi ajustado no meio do caminho, o que entrou em pauta para maio.]
```

---

## 🔴 REGRAS OBRIGATÓRIAS — TOM DE VOZ

- **Primeira pessoa do plural:** "Nós subimos", "Pausamos", "Redistribuímos". Nunca "o gestor fez", nunca "foi feito".
- **Sem tabelas frias.** Narrar em texto corrido como um humano escreveria para o cliente ler.
- **Sempre explicar o porquê.** Uma ação sem justificativa é ruído. "Pausamos o anúncio X" sozinho não vale — tem que virar "Pausamos o anúncio X porque a frequência subiu a 3,4 e o CPL dobrou em 4 dias, sinal de saturação do público."
- **Nicho saúde:** respeitar diretrizes dos conselhos. Não usar promessa de cura, sensacionalismo, garantia de resultado. Tom de autoridade, educação e acolhimento.
- **Sem métricas de vaidade como protagonistas.** Quando citar números, ancorar no que importa: CPL qualificado, volume de agendamento, conversas geradas.

---

## 🔴 REGRAS OBRIGATÓRIAS — O QUE É E O QUE NÃO É STATUS REPORT

✅ **Escopo (ações do gestor):**
- Criativos subidos (nome, campanha, ângulo, justificativa)
- Criativos pausados (com motivo — frequência, CPL, baixo CTR)
- Novas campanhas ou conjuntos estruturados
- Ajustes de orçamento (escala ou corte, com justificativa)
- Mudanças de público, exclusões, lookalikes novos
- Testes A/B iniciados e fechados
- Ajustes de copy, CTA, destino do anúncio
- Conversas com o cliente que redirecionaram a estratégia

❌ **Fora do escopo (não entra no status report):**
- Diagnóstico puro de performance (isso vai em relatório semanal, outra skill)
- Recomendações futuras / próximos passos (isso vai em relatório semanal)
- Métricas isoladas sem ação por trás
- Assuntos comerciais (follow-up de lead, script de atendimento, taxa de agendamento pós-conversa)

---

## 🔴 REGRAS OBRIGATÓRIAS — TOM DE ARGUMENTO COM BASE NO CONTEXTO

Os argumentos e palavras escolhidas devem respeitar o contexto comercial de cada cliente listado na tabela:

✅ **CONTEXTO POSITIVO** (ex.: Graciela Machado, Dra. Érica Marchiori, Fernanda Encinas)
- Palavras animadoras, sem hipérbole
- Sentimento: continuidade e evolução

❌ **CONTEXTO NEGATIVO / PRIORIDADE** (ex.: Dr. Fernando Bezerra, Dr. Diego Alencar, Dr. Higner Forastieri, Dr. Marcelo Bezerra)
- Palavras que validam a dificuldade, **sem** cunho pejorativo nem cenário irrecuperável
- Sentimento: turbulência com controle e atenção sendo aplicados

---

## 🔴 REGRAS OBRIGATÓRIAS — CASOS ESPECIAIS

- **Dr. Laureano Filho:** toda a narrativa só cobre Google Ads. Ignorar Meta. Se o Reportei indicar que a campanha esteve pausada em alguma semana, registrar: "Semana sem operação ativa — campanha Google em pausa conforme combinado com o cliente em [data da reunião]."
- **Semana sem sinais em nenhuma fonte:** escrever "Semana de manutenção — acompanhamos a performance sem intervenções estruturais." É preferível isso a inventar ação.
- **Cliente sem transcrição encontrada no Drive:** registrar mentalmente que o bloco vai depender 100% dos sinais Reportei e ser mais conservador nas inferências. Marcar no draft entregue ao gestor os blocos que ficaram sem lastro qualitativo para ele complementar.

---

## 🧭 ORDEM DE PREENCHIMENTO SUGERIDA (PRIORIDADE)

1. Dr. Fernando Bezerra (prioridade — baixo volume)
2. Dr. Diego Alencar (CPL crítico)
3. Dr. Marcelo Bezerra (atenção)
4. Dr. Higner Forastieri (CPM alto)
5. Dra. Mariângela Santiago
6. Dr. Caio Fernandes
7. Dr. Laureano Filho (só Google)
8. Dra. Nicolli
9. Fernanda Encinas
10. Graciela Machado
11. Dra. Érica Marchiori

Rodar os passos 3 e 4 em paralelo para todos os clientes; o preenchimento final (passo 8) segue esta ordem para que, se algo precisar pausar, os clientes mais sensíveis já estejam prontos.

---

## ✅ CHECKLIST PRÉ-EXECUÇÃO

Antes de rodar, confirmar:
- [ ] Datas das 9 semanas conferidas no calendário real
- [ ] Lista de 11 clientes ativa (Fernando Mattioli fora, Laureano só Google)
- [ ] Contexto comercial ainda válido (atualizar a tabela se o gestor sinalizou mudança)
- [ ] Acesso ao MCP Reportei funcionando (`list_projects` retorna)
- [ ] Acesso ao Gmail funcionando (`from:gemini subject:Anotações` retorna resultados no período)
- [ ] Acesso ao Google Drive funcionando (abrir link de doc anexado no e-mail do Gemini)
- [ ] ID do doc "Status Report - Gustavo" no ClickUp localizado e `page_id` das 11 subpáginas em mãos
- [ ] Contador de rate limit do Reportei zerado

---

## 📤 ENTREGA FINAL

Ao fim da execução:
1. Mensagem consolidada ao Gustavo: "Preenchi as 11 subpáginas do Status Report (março + abril/2026, 9 semanas). Links: [lista com link de cada subpágina]."
2. Flag explícito dos blocos que ficaram sem lastro qualitativo (sem transcrição encontrada) para revisão humana.
3. Lista de quaisquer sinais do Reportei que o sistema não conseguiu interpretar com confiança (ex.: salto de investimento sem contexto na transcrição) — para o gestor decidir se escreve à mão ou se tudo bem deixar como está.
