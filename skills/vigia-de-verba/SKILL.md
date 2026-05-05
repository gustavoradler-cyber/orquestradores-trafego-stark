name: vigia-de-verba
description: Notifica o gestor quando o gasto diário de qualquer anúncio Meta Ads ultrapassa o teto definido para o cliente. Captura overspend antes de virar problema. Roda diariamente. Triggers - "criar automação budget pacer", "vigia de verba", "alerta de gasto", "controle de orçamento meta ads", "overspend alert".
---

# Skill: Vigia de Verba (Budget Pacer — Versão Saúde)

Manda notificação quando o gasto **do dia** de qualquer anúncio passa do teto definido. Captura corrida de verba antes que vire problema — útil quando o algoritmo do Meta acelera entrega no fim do dia ou quando ad set estoura por má configuração de bid.

---

## Quando usar

Em todas as contas, mas é **especialmente crítico** em:
- Contas com orçamento mensal abaixo de R$ 5.000 — onde overspend de 1 dia compromete o mês inteiro
- Clientes em fase de validação (primeiros 60 dias) com orçamento conservador
- Contas com múltiplas campanhas simultâneas onde fica difícil vigiar manualmente

---

## Cálculo do threshold por cliente

Esta automação NÃO usa benchmarks de especialidade — usa o orçamento real do cliente. Fórmula:

```
Threshold diário = (orçamento mensal ÷ 30) × 1,8
```

O multiplicador 1,8 dá margem para um dia "rico" (algoritmo acelerou, criativo bombou) sem disparar falso positivo, mas captura overspend real (>2× o pacing).

Tabela de exemplo:

| Orçamento mensal cliente | Threshold de alerta diário |
|---|---|
| R$ 3.000 | R$ 180 |
| R$ 5.000 | R$ 300 |
| R$ 8.000 | R$ 480 |
| R$ 12.000 | R$ 720 |
| R$ 20.000 | R$ 1.200 |
| R$ 30.000 | R$ 1.800 |

**Importante:** o threshold é por **anúncio individual**, não por conta. Se a conta inteira gastar 1,8× o pacing num dia, o problema é maior — para isso, criar uma segunda automação no nível de account spend (ver variante abaixo).

---

## Prompt para criar a automação

Modelo:

```
Crie uma automação chamada "Vigia de Verba — [Cliente]" que roda diariamente
às 18h. Notificar se qualquer anúncio ativo gastou mais de R$ [X] hoje.
Quero saber antes que o overspend saia de controle.
```

Exemplo — cliente com orçamento mensal R$ 8.000:

```
Crie uma automação chamada "Vigia de Verba — Marcus Calazans" que roda diariamente
às 18h. Notificar se qualquer anúncio ativo gastou mais de R$ 480 hoje.
```

---

## Variante 1 — Vigia no nível de conta (account spend)

Para captar overspend agregado (vários anúncios pequenos somando muito):

```
Crie uma automação chamada "Vigia de Conta — [Cliente]" que roda diariamente
às 18h. Notificar se o gasto total da conta hoje ultrapassou R$ [Y].
```

Onde `Y = (orçamento mensal ÷ 30) × 1,4` — threshold mais conservador no nível agregado.

---

## Variante 2 — Vigia em horário de risco (08h e 14h)

Em vez de uma única checagem às 18h (já é tarde demais), criar 2 verificações ao longo do dia:

```
Crie uma automação chamada "Vigia de Verba 08h — [Cliente]" que roda diariamente
às 8h. Notificar se qualquer anúncio ativo já gastou mais de R$ [X/3] desde a meia-noite.
```

```
Crie uma automação chamada "Vigia de Verba 14h — [Cliente]" que roda diariamente
às 14h. Notificar se qualquer anúncio ativo já gastou mais de R$ [X×0.7] desde a meia-noite.
```

A primeira (08h) pega anúncios que estouraram durante a madrugada — momento de maior risco porque ninguém está vigiando. A segunda (14h) pega ritmo do dia.

---

## Configuração técnica resultante

| Trigger | Performance Threshold (service: meta-ads) |
|---|---|
| **Conditions** | spend > [valor] (today's spend) |
| **Lookback** | Today (0 days) |
| **Aggregation** | Per ad |
| **Action** | Notify |
| **Frequency** | Daily às 18h |

---

## Por que pausar é arriscado aqui (e por isso usamos NOTIFY)

A tentação é fazer auto-pause quando o anúncio "estoura". Não fazer. Razões:

1. **Falso positivo é caro.** Anúncio bom que gastou bem hoje pode estar trazendo conversa em escala. Pausar sem revisar = matar campeão.

2. **O Meta tem algoritmo de pacing próprio.** Algumas vezes ele "acelera" entrega num dia para compensar dia anterior fraco. Isso não é overspend — é pacing inteligente. Auto-pause prejudica.

3. **NOTIFY dá controle ao gestor.** Recebeu alerta → 30 segundos para decidir se pausa, reduz orçamento, ou deixa passar.

---

## Considerações de compliance

Vigia de verba é puramente operacional/financeira. Sem implicação regulatória.

---

## Quando NÃO usar

- Contas com **CBO (Campaign Budget Optimization)** habilitado — o orçamento flui entre ads/ad sets, então vigia por ad individual gera ruído. Use a Variante 1 (nível de conta) em vez disso.
- Em campanhas com objetivo "Reach" ou "Awareness" sem meta de conversão — overspend é feature, não bug.
- Em contas com orçamento mensal acima de R$ 50.000 — variação diária natural torna threshold fixo inviável. Use percentil dinâmico (P95 do gasto histórico).