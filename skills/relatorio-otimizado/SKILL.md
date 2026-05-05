---
name: relatorio-semanal-reportei
description: Gera o relatório semanal de tráfego pago para os 11 clientes da Stark via MCP Reportei (roda toda quarta-feira). Pré-requisito Prompt B executado no mesmo dia. Triggers - "relatório semanal", "relatório do reportei", "rodar relatórios da semana", "gerar timelines da semana", "relatório de tráfego semanal", "marco da timeline".
---

# Skill: Relatório Semanal Reportei (Stark)

Prompt principal — roda toda QUARTA-FEIRA. Pré-requisito: Prompt B (Context Updater) executado no mesmo dia.

---

## VARIÁVEIS DE DATA — CALCULADAS AUTOMATICAMENTE

A janela é derivada da data de execução (sempre quarta-feira). Não preencher manualmente.

```
HOJE                  = data de execução (quarta-feira)
API_DATE_END          = HOJE - 1 dia            (terça anterior)
API_DATE_START        = HOJE - 7 dias           (quarta anterior)
API_COMPARISON_END    = API_DATE_START - 1 dia  (terça retrasada)
API_COMPARISON_START  = API_DATE_START - 7 dias (quarta retrasada)

PERIODO_LABEL  = "Semana de {DD/MM PERIODO_INICIO} a {DD/MM PERIODO_FIM}"
PERIODO_INICIO = DD/MM da API_DATE_START
PERIODO_FIM    = DD/MM da API_DATE_END
COMP_INICIO    = DD/MM da API_COMPARISON_START
COMP_FIM       = DD/MM da API_COMPARISON_END
```

Exemplo (execução em 29/04/2026, quarta):

- Período: 22/04 → 28/04
- Comparativo: 15/04 → 21/04

**Override manual:** se quiser rodar uma janela diferente da padrão, sobrescreva `PERIODO_INICIO/FIM` e `API_DATE_START/END` manualmente no topo antes de executar.

---

## CONFIGURAÇÕES FIXAS

- Template Reportei: "Relatório 2.0 - PADRÃO" (ID: `146208`). Usar ID se não encontrar pelo nome.
- Cliente excluído: Dr. Fernando Mattioli - FACE (`project_id: 772702`).
- Cliente Google-only: Dr. Laureano Filho (`project_id: 982754`) — relatório apenas com dados Google Ads/Analytics.
- Pasta consolidada de contexto: "Contexto Clientes - Stark" no Drive (alimentada pelos Prompts A e B).

---

## CLIENTES ATIVOS

O "contexto comercial" de cada cliente NÃO está mais escrito aqui. É puxado dinamicamente do doc "Contexto - {Cliente}" na pasta consolidada — ver Passo 0.

---

## CONTROLE DE RATE LIMIT

- Limite Reportei: 40 requisições por janela de 9 minutos.
- Ao atingir 38 requisições, usar `ScheduleWakeup` com `delaySeconds: 540`.
- Maximizar chamadas paralelas em cada batch.

Distribuição orientativa:

- Janela 1: `list_projects` + 11× `list_integrations` + 11× `create_report` + 11× `get_report` + 5× `get_project_metrics` (~40).
- Janela 2: 6× `get_project_metrics` restantes + 11× `create_timeline_event` (~17).

---

## PASSOS DE EXECUÇÃO

### PASSO 0 — Pré-relatório: ler contexto consolidado (11 chamadas paralelas Drive)

Para cada cliente, abrir doc "Contexto - {Cliente}" na pasta consolidada e extrair:

- Seção "Perfil & Especialidade" — usar como base interpretativa.
- Seção "Momento Comercial Atual" — substitui o antigo "contexto comercial" hardcoded.
- Seção "Pontos de Atenção Recorrentes" — alimentar o tom dos próximos passos.

Classificar sentimento geral do contexto em **POSITIVO | NEUTRO | NEGATIVO** usando regra:

- **NEGATIVO** se houver palavras como: queda, cancelamento, baixo volume, saturação, desistência, urgência, crítico.
- **POSITIVO** se houver: crescimento, recorde, expansão, fechamento alto, escalada, pleno.
- **NEUTRO** caso contrário.

Persistir em memória local (cliente, sentimento, perfil, momento_atual, pontos_atencao) para uso nos passos 5 e 7.

### PASSO 1 — Listar projetos e template (2 chamadas paralelas)

- `list_projects` → confirmar IDs dos 11 clientes, excluir 772702.
- `list_templates` → localizar template ID 146208.

### PASSO 2 — Listar integrações (11 chamadas paralelas)

- Para todos os 11 clientes simultaneamente: `list_integrations(project_id: X)`.
- Anotar canais por cliente: `meta_ads`, `google_adwords`, `google_analytics_4`, `instagram`.

### PASSO 3 — Criar relatórios (11 chamadas paralelas)

Para todos os 11 clientes, `create_report` com:

```
template_id:           146208
date_start:            {API_DATE_START}
date_end:              {API_DATE_END}
comparison_date_start: {API_COMPARISON_START}
comparison_date_end:   {API_COMPARISON_END}
```

### PASSO 4 — Buscar métricas (chamadas em batches)

- Para cada cliente: `get_project_metrics` + `get_report`.
- Se timeout em alguma integração (especialmente GA4), documentar como "sem dados disponíveis" e seguir.

### PASSO 5 — Criar marco na timeline (11 chamadas paralelas)

- Para cada cliente, `create_timeline_event` com texto montado conforme template no final deste prompt.
- Aplicar regras de tom segundo o sentimento extraído no PASSO 0.

### PASSO 6 — WhatsApp (sem chamadas API)

- Gerar mensagem de WhatsApp para cada cliente conforme formato no final deste prompt.

### PASSO 7 — Append "Aprendizados" no doc de contexto (11 chamadas paralelas Drive)

Ao final de tudo, abrir o doc "Contexto - {Cliente}" na pasta consolidada e ACRESCENTAR (append) na seção "Aprendizados de Tráfego (append-only)" o seguinte bloco:

```
### Semana {PERIODO_INICIO} → {PERIODO_FIM}
- Investimento: R$ X
- CPL: R$ X (vs R$ Y na semana anterior, {↑/↓ Z%})
- Conversas: X (vs Y, {↑/↓ Z%})
- Hipótese principal validada/quebrada: [1 frase]
- Sinal a monitorar próxima semana: [1 frase]
- Ação executada: [1 frase]
```

- NÃO mexer em outras seções.
- NÃO atualizar o campo "Última Atualização" (responsabilidade do Prompt B).
- Sempre append (nunca substituir blocos antigos).

---

## REGRAS DE RENDERIZAÇÃO REPORTEI (BLOQUEANTES)

Estas regras são bloqueantes. Aplicar SEMPRE ao gerar o HTML do marco de timeline — caso contrário a renderização no Reportei quebra ou aparece como bloco denso ilegível.

1. **NÃO USAR EMOJIS NUMÉRICOS** (`1⃣`, `2⃣`, `3⃣`) antes de TOFU/MOFU/BOFU. Esses caracteres compostos renderizam como mojibake (caractere quebrado) no Reportei. Manter apenas o nome da etapa em `<strong>` — ex.: `<strong>Topo de Funil (TOFU)</strong>`, `<strong>Meio de Funil (MOFU)</strong>`, `<strong>Fundo de Funil (BOFU)</strong>`.

2. **SEPARAR BLOCOS COM `<p>&nbsp;</p>`** — o Reportei NÃO renderiza margem entre tags `<p>` consecutivas. Para criar respiro visual entre seções principais (Visão Geral, Investimento, cada etapa do funil, Google Ads, Próximos Passos), inserir uma linha em branco explícita usando `<p>&nbsp;</p>`. Não usar `<br>` isolado para esse fim.

3. **ESTRUTURA OBRIGATÓRIA NA ORDEM:** Visão Geral → Investimento Total + CPL com benchmark → Análise do Funil (TOFU → MOFU → BOFU, cada uma com dados + PoP % vs. semana anterior + análise qualitativa) → Alertas (⚠️) quando aplicável → Google Ads (seção SEPARADA, somente se o cliente usa) → Próximos Passos (com `→` no início de cada ação).

4. **ALERTAS sem hipérboles.** Formato fixo: `⚠️ [o que aconteceu] + [por que] → [ação em andamento ou planejada]`. Reportar problema sem indicar contramedida é proibido.

5. **HTML PERMITIDO** restrito a: `<h2>`, `<h3>`, `<p>`, `<strong>`, `<b>`, `<br>`, `<a>`. NUNCA usar CSS inline, classes, `<div>`, `<span>` com estilo — o Reportei strip-a tudo isso.

---

## TEMPLATE DO MARCO DE TIMELINE

**Título:**

```
Desempenho do Tráfego | {PERIODO_INICIO} a {PERIODO_FIM}
```

**Estrutura HTML:**

```html
<h2>📊 Relatório de Performance {PERIODO_INICIO} a {PERIODO_FIM}</h2>
<p>&nbsp;</p>
<h3>🚀 Visão Geral do Período</h3>
<p>[Resumo executivo: principais números, destaque positivo e ponto de
    atenção. Inclua o contexto comercial puxado do PASSO 0 (Momento
    Comercial Atual). 3–5 frases.]</p>
<p><strong>Investimento Total Meta:</strong> R$ X<br>
<strong>Custo Médio por Lead (Meta):</strong> R$ X — [avaliação vs
    benchmark]</p>
<p>&nbsp;</p>
<h3>🔍 Análise do Funil de Vendas (Meta)</h3>
<p>&nbsp;</p>
<p><strong>Topo de Funil (TOFU) — Atração e Alcance</strong><br>
Alcance: X | Impressões: X | Frequência: X | PoP: ↑/↓ X% vs. semana anterior<br>
Novos Seguidores: +X | Custo por Seguidor: R$ X<br>
[Análise: frequência, organic vs paid reach, reels, stories]</p>
<p>&nbsp;</p>
<p><strong>Meio de Funil (MOFU) — Maturidade da Audiência</strong><br>
[VER REGRAS OBRIGATÓRIAS DE MOFU ABAIXO — leitura é em estágio de
 maturidade, NÃO em gargalo mecânico]</p>
<p>&nbsp;</p>
<p><strong>Fundo de Funil (BOFU) — Conversão Direta</strong><br>
X conversas iniciadas | CPL R$ X | PoP: ↑/↓ X% vs. semana anterior<br>
[Análise do volume e custo por conversa. Diagnóstico e contexto.]</p>

[SEÇÃO GOOGLE ADS — incluir apenas se o cliente tiver google_adwords
 com dados:]
<h3>🔵 Google Ads</h3>
<p>&nbsp;</p>
<p>Investimento: R$ X | X conversões | CPL R$ X | CTR X% | X cliques</p>
<p>[Análise breve]</p>
<p>&nbsp;</p>
<h3>📈 Próximos Passos &amp; Otimizações</h3>
<p>→ [ação 1]<br>
→ [ação 2]<br>
→ [ação 3]</p>
<p>&nbsp;</p>
<p>👉 <a href="{URL_RELATORIO}">Acesse o Relatório Completo</a></p>
```

---

## REGRAS OBRIGATÓRIAS — SEÇÃO MOFU (REVISADAS: JORNADA & QUALIFICAÇÃO)

A seção MOFU é interpretada como **ESTÁGIO DE MATURIDADE DA AUDIÊNCIA NA JORNADA DO PACIENTE** — não como gargalo mecânico de funil. As fórmulas técnicas continuam válidas; muda a leitura interpretativa.

### Princípios da nova leitura

- **Aquecimento e nutrição:** quanto a audiência foi exposta a conteúdo educativo antes de decidir iniciar uma conversa.
- **Construção de autoridade:** repetições qualificadas, prova social, presença consistente do médico/cirurgião.
- **Qualificação implícita:** quem clica e permanece já demonstrou nível de intenção; o objetivo é refinar a qualidade dessa porta de entrada.
- **Educação como ativo:** conteúdo educativo é o que move o paciente do desconhecimento para a consideração informada.

### O que SEMPRE buscar

- Dados específicos de campanhas/conjuntos de MOFU (quando existirem).
- Métricas de retenção em conteúdo educativo (stories, reels, vídeos longos).
- Proporção entre alcance orgânico e pago como sinal de maturação da audiência.
- NUNCA deixar a seção MOFU como "sem dados isolados" — sempre fazer inferências.

### Fórmulas (mantidas)

```
Cliques estimados      = Impressões × (CTR ÷ 100)
CPC estimado           = Investimento ÷ Cliques estimados
CPM                    = (Investimento ÷ Impressões) × 1000
Taxa clique→conversa   = Conversas ÷ Cliques estimados
```

### Leitura por situação

**Quando há CTR disponível:**

- CTR ≥ 2,5%: o criativo está cumprindo o papel de conduzir o paciente para o próximo estágio de consideração — entrada de qualidade.
- CTR < 2%: o conteúdo de entrada ainda não está educando/atraindo a fatia certa da audiência. Não é falha técnica de criativo isolada — é descompasso entre a mensagem e o estágio em que o paciente está.
- Taxa clique→conversa ≥ 2%: audiência madura no momento do clique.
- Taxa clique→conversa < 1%: o paciente clica curioso mas ainda não está pronto para iniciar conversa — sinal de que o aquecimento prévio precisa de mais profundidade educativa antes da abordagem direta.
- Ambos abaixo do ideal: a audiência não está sendo nutrida o suficiente antes da abordagem de conversão — reforçar conteúdo de autoridade médica e educação clínica nos estágios anteriores.

**Quando NÃO há CTR (cliente sem campanhas ativas de MOFU):**

- Retenção de stories como proxy de qualificação orgânica (≥ 40% indica audiência engajada e em consideração).
- Engagement rate em reels como sinal de receptividade ao conteúdo educativo.
- Proporção alcance orgânico vs pago — quando o orgânico cresce, indica que a audiência madura está retornando espontaneamente.
- A base orgânica aquecida representa o estoque de pacientes em qualificação — leitura é sobre QUALIDADE da base, não sobre volume bruto.

**Quando frequência é o fator dominante:**

- Frequência alta (> 2,5) com CPL estável: re-exposição está funcionando como nutrição — o paciente precisa de mais pontos de contato antes de decidir.
- Frequência alta (> 2,5) com CPL crescente: a re-exposição saturou — diversificar o conteúdo educativo, não apenas o público.
- Frequência ideal (1,0–2,5): audiência convertendo em poucas exposições — sinal de segmentação precisa E de que o conteúdo de entrada já chega ao paciente certo.

### Benchmarks de referência

- CTR campanhas de conversão: ≥ 2,5%
- Taxa clique→conversa: 2–4%
- Frequência: ideal 1,0–2,5 | alerta > 2,5 | crítico > 3,5
- CPM medicina estética: R$ 10–18
- Retenção stories: ideal ≥ 40%

### Vocabulário a usar

- "estágio de maturidade da audiência"
- "aquecimento prévio"
- "nutrição com conteúdo educativo"
- "construção de autoridade"
- "consideração informada"
- "intenção qualificada"
- "qualidade do tráfego pós-clique"
- "audiência em qualificação"

### Vocabulário a EVITAR

- "gargalo de funil" / "funil furado"
- "fricção pós-clique" — substituir por "fricção no estágio de consideração"
- "conversão direta" como métrica única do MOFU
- Vocabulário binário tipo "passa / não passa"

---

## REGRAS OBRIGATÓRIAS — PRÓXIMOS PASSOS

Os próximos passos focam EXCLUSIVAMENTE em tráfego. São recomendações técnicas de mídia, conteúdo e otimização de campanhas.

### PERMITIDO (tráfego)

- Criativos: pausar, criar, testar novos formatos, A/B test.
- Audiências: lookalike, broad, expansão, exclusões, listas.
- Orçamento: escalar, redistribuir, otimizar bid.
- Frequência e CPM: diversificar públicos para reduzir.
- CTR: formatos de maior performance, copy de anúncio.
- Remarketing e retargeting: ativar para cliques sem conversão, visitantes de perfil, novos seguidores.
- Conteúdo orgânico: cadência de reels e posts como alavanca de alcance e qualificação.
- Canais: Google Ads, palavras-chave, expansão de cobertura, TikTok.
- Fluxo pós-clique: CTA do botão, destino do anúncio, direct link.

### PROIBIDO (comercial — não mencionar)

- Estruturar processo de atendimento.
- Script de follow-up ou recontato.
- Protocolo de resposta a leads.
- Taxa de conversão conversa → agendamento.
- Atendimento rápido / tempo de resposta.
- Qualquer recomendação sobre o que fazer com os leads APÓS a conversa ser iniciada.

---

## REGRAS OBRIGATÓRIAS — TOM DE ARGUMENTO COM BASE NO CONTEXTO

Os argumentos da timeline DEVEM levar em consideração o sentimento extraído no PASSO 0 a partir do doc de contexto consolidado do cliente.

### CONTEXTO POSITIVO

- Uso de palavras: animadoras e positivas, mas SEM hipérboles.
- Sentimento geral do texto: continuidade e evolução do projeto.

### CONTEXTO NEUTRO

- Uso de palavras: descritivas e técnicas; foco em fatos.
- Sentimento geral do texto: estabilidade com pontos a monitorar.

### CONTEXTO NEGATIVO

- Uso de palavras: validar a situação, MAS NUNCA hipérboles ou termos pejorativos. Não usar palavras que sugiram cenário irrecuperável ou desastroso.
- Sentimento geral do texto: turbulência e dificuldades, mas com controle e atenção devidamente aplicados.

---

## FORMATO MENSAGEM WHATSAPP

Gerar uma mensagem por cliente ao final.

```
*Relatório {PERIODO_LABEL} | {PERIODO_INICIO} a {PERIODO_FIM}*

📊 *Meta Ads*
• Investimento: R$ X
• Conversas iniciadas: X
• CPL: R$ X
• Alcance: X | Frequência: X
• CTR: X%

[Se tiver Google Ads:]
📊 *Google Ads*
• Investimento: R$ X
• Conversões: X | CPL: R$ X
• CTR: X%

📱 *Instagram*
• Visualizações: X
• Alcance: X
• Novos seguidores: +X
• Reels: X vídeos | X views

📎 Relatório completo: {URL_RELATORIO}
```

---

## CHECKLIST PRÉ-EXECUÇÃO

- Prompt B (Context Updater) RODADO HOJE? — pré-requisito obrigatório.
- Datas calculadas conferem com a quarta-feira de execução.
- Contagem de rate limit Reportei zerada para esta sessão.
- Pasta consolidada "Contexto Clientes - Stark" tem doc para os 11 clientes ativos.