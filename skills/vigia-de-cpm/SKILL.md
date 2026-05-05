name: vigia-de-cpm
description: Pausa anúncios Meta Ads onde o CPM (custo por mil impressões) excedeu o teto da especialidade. CPM alto sinaliza saturação de audiência ou baixa relevância — em saúde, geralmente indica criativo cansado. Roda diariamente. Triggers - "criar automação cpm guardian", "vigia de cpm", "pausar anúncio com cpm alto", "cpm saturado", "controle de cpm meta ads".
---

# Skill: Vigia de CPM (CPM Guardian — Versão Saúde)

Pausa qualquer anúncio em que o CPM (custo por mil impressões) subiu acima do teto definido para a especialidade. CPM alto significa que você está pagando caro demais para entregar o anúncio — em saúde, isso quase sempre indica saturação de audiência ou criativo cansado/irrelevante.

---

## Quando usar

Em todas as contas com investimento mensal acima de R$ 3.000. Em contas menores, o volume de impressões pode ser baixo demais pra sinalizar saturação real, gerando falsos positivos.

Especialmente útil em:
- Períodos de leilão acirrado (carnaval, fim de ano, datas comemorativas tipo "Outubro Rosa")
- Campanhas de retargeting com audiências pequenas (< 50.000 pessoas)
- Criativos rodando há mais de 21 dias

---

## Configuração por especialidade (CPM teto)

CPM benchmark Brasil — saúde, calibrado por nicho:

| Especialidade | CPM saudável | CPM alerta (pausa) |
|---|---|---|
| Cirurgia Plástica | R$ 12 — R$ 22 | > R$ 28 |
| Cirurgia Plástica (face) | R$ 15 — R$ 25 | > R$ 32 |
| Cirurgia Ortognática | R$ 18 — R$ 30 | > R$ 38 |
| Dermatologia | R$ 10 — R$ 18 | > R$ 24 |
| Medicina Estética | R$ 12 — R$ 22 | > R$ 28 |
| Tricologia | R$ 14 — R$ 24 | > R$ 30 |
| Implantes Dentários | R$ 16 — R$ 28 | > R$ 35 |
| Emagrecimento | R$ 18 — R$ 30 | > R$ 38 |
| Oncologia | R$ 22 — R$ 38 | > R$ 48 |
| Cirurgia Cabeça e Pescoço | R$ 20 — R$ 35 | > R$ 45 |
| Anestesia | R$ 18 — R$ 32 | > R$ 42 |
| Médico de Família | R$ 8 — R$ 16 | > R$ 22 |
| Saúde Geral | R$ 10 — R$ 20 | > R$ 26 |

**Regra:** CPM alerta = ~30% acima do teto saudável da especialidade.

---

## Prompt para criar a automação

Modelo:

```
Crie uma automação chamada "Vigia de CPM — [Cliente]" que roda diariamente
às 9h. Pausar qualquer anúncio ativo onde o CPM está acima de R$ [X] nos
últimos 3 dias e o gasto é de pelo menos R$ 20.
```

Exemplo — Dr. Marcus Calazans (Cirurgia Plástica):

```
Crie uma automação chamada "Vigia de CPM — Marcus Calazans" que roda diariamente
às 9h. Pausar qualquer anúncio ativo onde o CPM está acima de R$ 28 nos últimos
3 dias e o gasto é de pelo menos R$ 20.
```

---

## Configuração técnica resultante

| Trigger | Performance Threshold (service: meta-ads) |
|---|---|
| **Conditions** | cpm > [valor] AND spend >= 20 (logic: AND) |
| **Lookback** | 3 dias |
| **Aggregation** | Per ad |
| **Action** | Pause |
| **Frequency** | Daily às 9h |

O `spend >= 20` evita pausar anúncios novos que ainda não estabilizaram o CPM (primeiros R$ 20 podem ter CPM artificialmente alto pelo aprendizado do algoritmo).

---

## Diagnóstico ao receber pause — fluxo de investigação

Quando esta automação pausar um anúncio, antes de simplesmente reativar, verificar:

1. **Saturação de audiência:** frequência > 2,5? Se sim, audiência está cansada — diversificar público antes de reativar.
2. **Qualidade do criativo (relevance score):** acessar Meta Ads Manager → relevância caiu? Trocar criativo.
3. **Concorrência sazonal:** período coincide com data comemorativa do nicho (ex.: pré-verão para corpo, pós-carnaval para face)? Pode ser temporário — aguardar 7 dias e reavaliar.
4. **Mudança de leilão:** outro player grande lançou campanha no nicho? Verificar Meta Ad Library do concorrente local.

---

## Considerações de compliance

CPM alto não tem implicação direta de compliance. A automação só pausa por critério de eficiência. Seguro rodar em modo totalmente automático.

---

## Variantes por etapa de funil

Em funil estruturado, criar automação separada por etapa porque CPM aceitável muda:

**TOFU:** CPM tende a ser **mais baixo** porque o algoritmo entrega para audiências amplas e baratas. Threshold de pause: subtrair R$ 5 do alerta da tabela.

**BOFU:** CPM tende a ser **mais alto** porque o público é qualificado e disputado. Threshold de pause: somar R$ 5 ao alerta da tabela.

**Retargeting (audiências < 50k):** CPM é naturalmente mais alto. Considerar threshold de pause 1,5× o valor da tabela.

---

## Quando NÃO usar

- Primeiros 7 dias de uma campanha nova — fase de aprendizado distorce CPM.
- Anúncios em audiência muito segmentada (< 10.000 pessoas) — CPM alto é estrutural, não defeito.
- Campanhas de **awareness com objetivo "Reach"** — pausar por CPM destrói o propósito da campanha.
<!-- atualizado em 05/05/2026 -->
