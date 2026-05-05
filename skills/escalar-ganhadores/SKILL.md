name: escalar-ganhadores
description: Identifica anúncios Meta Ads com CPL abaixo do benchmark da especialidade e volume relevante, e escala a verba do conjunto de anúncios. Modo padrão é NOTIFY (gestor aprova antes de escalar) por compliance — auto-scale só com criativos pré-aprovados. Triggers - "criar automação winner scaler", "escalar anúncios ganhadores", "automação de escala", "scale winners saúde", "escalar ad bom CPL".
---

# Skill: Escalar Ganhadores (Winner Scaler — Versão Saúde)

Encontra anúncios com CPL bom e volume relevante de conversas, e propõe escala da verba do ad set. Roda diariamente para que vencedores ganhem mais combustível automaticamente.

**Diferença crítica vs. e-commerce:** em saúde, escalar verba sem revisão humana é arriscado. Se o criativo tiver problema de compliance (promessa de cura, antes/depois sem disclaimer, sensacionalismo) e a automação escalar, multiplica o risco regulatório (CFM/CRM/CRO). Por isso o modo padrão desta automação é **NOTIFY** — gestor aprova antes.

---

## Quando usar

Após você ter criativos rodando há pelo menos 14 dias e identificado padrões claros de performance. Para clientes em fase de validação (primeiros 30 dias), use modo manual — não automatize escala.

---

## Configuração por especialidade

Tabela de threshold de CPL para qualificar como "ganhador" (lookback 7 dias, spend mínimo R$ 50):

| Especialidade | CPL ganhador (≤) | Conversas mínimas (7d) |
|---|---|---|
| Cirurgia Plástica | R$ 10 | 5 |
| Cirurgia Plástica (face) | R$ 13 | 4 |
| Cirurgia Ortognática | R$ 35 | 3 |
| Dermatologia | R$ 18 | 4 |
| Medicina Estética | R$ 40 | 3 |
| Tricologia | R$ 25 | 3 |
| Implantes Dentários | R$ 40 | 3 |
| Emagrecimento | R$ 50 | 3 |
| Oncologia | R$ 70 | 2 |
| Cirurgia Cabeça e Pescoço | R$ 60 | 2 |
| Anestesia | R$ 55 | 2 |
| Médico de Família | R$ 20 | 4 |
| Saúde Geral | R$ 28 | 4 |

**Regra:** CPL ganhador = ~70% do teto do benchmark da especialidade. Conversas mínimas garantem que não escala em cima de 1 conversa sortuda.

---

## Modos de operação

### Modo A — NOTIFY (RECOMENDADO — padrão Stark)

Automação detecta candidatos a escala e te notifica no ClickUp/Slack. Você revisa criativo (compliance + estética) e escala manualmente, ou aprova via comando para a skill `otimizar-campanhas` executar.

```
Crie uma automação chamada "Winner Alert — [Cliente]" que roda diariamente
às 10h. Encontrar todos os anúncios ativos com CPL abaixo de R$ [X] nos
últimos 7 dias que tiveram pelo menos [N] conversas iniciadas e gastaram
no mínimo R$ 50. Notificar com lista de candidatos a escala.
```

### Modo B — AUTO-SCALE conservador (para criativos pré-aprovados)

Apenas para criativos taggeados como "compliance:aprovado" no Meta Ads (label/tag). Escala 10% do orçamento do ad set por execução, máximo 1× por semana por ad set.

```
Crie uma automação chamada "Winner Scaler — [Cliente]" que roda toda
segunda-feira às 10h. Encontrar anúncios ativos com label "compliance-aprovado",
CPL abaixo de R$ [X] nos últimos 7 dias, com pelo menos [N] conversas e
spend mínimo R$ 50. Aumentar orçamento do ad set em 10%.
```

**Cap obrigatório:** orçamento do ad set não pode ultrapassar 2× o orçamento original (escala máxima de 100% acumulada). Após atingir o teto, automação para de escalar e notifica.

---

## Configuração técnica resultante (Modo A — Notify)

| Trigger | Performance Threshold (service: meta-ads) |
|---|---|
| **Conditions** | cost_per_messaging_conversation < [X] AND messaging_conversation_started_7d >= [N] AND spend >= 50 (logic: AND) |
| **Lookback** | 7 dias |
| **Aggregation** | Per ad |
| **Action** | Notify |
| **Frequency** | Daily às 10h |

---

## Considerações de compliance — IMPORTANTE

Escalar verba multiplica o alcance do anúncio. Se o anúncio tem qualquer um destes problemas, escalar = multiplicar risco regulatório:

- Promessa de resultado garantido ou cura
- Antes/depois sem disclaimer adequado (CFM Resolução 1.974/2011)
- Linguagem sensacionalista ("milagroso", "transformação chocante")
- Depoimento de paciente sem autorização escrita
- Imagem clínica de procedimento (CFM proíbe para fim publicitário)
- CRM/CRO do profissional não exibido
- Tratamento de doença sendo apresentado como "estética" (oncologia, endocrinologia, dermatologia)

**Antes de aprovar qualquer escala, rodar a skill `avaliar-criativo` no anúncio candidato.** Se a skill aprovar, escala. Se sinalizar risco, recusa.

---

## Variantes por etapa de funil

Em funil estruturado (TOFU/MOFU/BOFU), o critério de "ganhador" muda:

**TOFU (seguidores):** CPF abaixo de R$ 1,50, mínimo 100 novos seguidores em 7 dias, spend mínimo R$ 100. Escala mais permissiva — risco de compliance é menor em criativos de awareness.

**MOFU (engajamento/consideração):** custo por visualização de página/link abaixo de R$ 0,80, retenção de vídeo > 50%, spend mínimo R$ 50. Escala média.

**BOFU (conversa):** o critério padrão da tabela acima. Escala mais conservadora — é onde mora o maior risco de compliance.

---

## Quando NÃO usar

- Primeiros 30 dias de uma conta — sem histórico para qualificar "ganhador" estatisticamente.
- Após mudança de criativo nos últimos 7 dias — o algoritmo ainda está reaprendendo.
- Em períodos sazonais atípicos (ex.: Black Friday em estética, lançamento de procedimento) — distorção temporária pode levar a escala em cima de pico não-recorrente.
- Conta com menos de 30 conversas/mês — volume insuficiente pra confiar no sinal.
