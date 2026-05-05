name: monitor-novos-criativos
description: Verifica anúncios Meta Ads criados nos últimos 7 dias e pausa os que estão claramente subperformando após período de aprendizado. Dá fair shot ao algoritmo mas corta perdedores antes de comprometer verba do mês. Triggers - "criar automação new ad monitor", "monitor novos criativos", "pausar criativo novo ruim", "fair shot criativo", "early kill anúncio novo".
---

# Skill: Monitor de Novos Criativos (New Ad Monitor — Versão Saúde)

Olha apenas para anúncios criados nos últimos 7 dias. Se algum deles gastou verba significativa sem entregar resultado mínimo após o período de aprendizado, pausa. Dá *fair shot* — não corta antes do algoritmo aprender — mas também não deixa um perdedor claramente ruim sangrar verba a semana inteira.

**Esta automação é diferente da `cortar-anuncios-sem-conversa`:** aquela aplica a anúncios maduros (já passaram da fase de aprendizado). Esta aplica especificamente a anúncios em fase de aprendizado, com thresholds **mais permissivos** porque o algoritmo ainda está calibrando.

---

## Quando usar

**Sempre que você sobe um lote de criativos novos.** Recomendo mantê-la sempre ativa — ela não gera ruído porque só atua em anúncios criados nos últimos 7 dias.

Especialmente útil em:
- Após rodada de testes da skill `testar-darkposts`
- Após rodada da skill `testar-agenda-conteudo`
- Lançamento de campanha nova (TOFU/MOFU/BOFU completo do zero)
- Substituição de criativos saturados (alerta da skill `saturacao-criativa`)

---

## Configuração por especialidade

Threshold do "fair shot": gasto e CPL aceitável durante a fase de aprendizado (até 7 dias). Pause acontece quando AMBAS as condições falham simultaneamente.

| Especialidade | Spend mínimo p/ avaliar | CPL teto na fase de aprendizado |
|---|---|---|
| Cirurgia Plástica | R$ 60 | R$ 25 (1,7× benchmark) |
| Cirurgia Plástica (face) | R$ 70 | R$ 30 |
| Cirurgia Ortognática | R$ 120 | R$ 80 |
| Dermatologia | R$ 80 | R$ 42 |
| Medicina Estética | R$ 130 | R$ 100 |
| Tricologia | R$ 90 | R$ 60 |
| Implantes Dentários | R$ 130 | R$ 92 |
| Emagrecimento | R$ 150 | R$ 130 |
| Oncologia | R$ 200 | R$ 170 |
| Cirurgia Cabeça e Pescoço | R$ 180 | R$ 150 |
| Anestesia | R$ 170 | R$ 135 |
| Médico de Família | R$ 80 | R$ 50 |
| Saúde Geral | R$ 90 | R$ 65 |

**Regra:** Spend mínimo = ~4× o piso do CPL benchmark da especialidade. CPL teto na fase de aprendizado = ~1,7× o teto do benchmark — porque criativo ainda imaturo pode ter CPL provisoriamente alto.

---

## Prompt para criar a automação

Modelo:

```
Crie uma automação chamada "Monitor de Novos Criativos — [Cliente]" que roda
diariamente às 9h. Verificar anúncios ativos criados nos últimos 7 dias. Se o
anúncio gastou mais de R$ [X] e o custo por conversa iniciada está acima de
R$ [Y], pausar.
```

Exemplo — Dr. Marcus Calazans (Cirurgia Plástica):

```
Crie uma automação chamada "Monitor de Novos Criativos — Marcus Calazans" que
roda diariamente às 9h. Verificar anúncios ativos criados nos últimos 7 dias.
Se o anúncio gastou mais de R$ 60 e o custo por conversa iniciada está acima
de R$ 25, pausar.
```

---

## Configuração técnica resultante

| Trigger | Performance Threshold (service: meta-ads) |
|---|---|
| **Conditions** | spend > [X] AND cost_per_messaging_conversation > [Y] (logic: AND) |
| **Lookback** | 7 dias |
| **Ad created within** | 7 dias |
| **Aggregation** | Per ad |
| **Action** | Pause |
| **Frequency** | Daily às 9h |

A condição `Ad created within 7 days` é o que diferencia esta automação da `cortar-anuncios-sem-conversa`. Aqui só monitoramos anúncios novos.

---

## Variante para TOFU (foco em seguidor, não conversa)

Em campanhas de topo (objetivo "novos seguidores" / awareness), o critério muda — não tem conversa pra qualificar. Use CPF (custo por seguidor):

```
Crie uma automação chamada "Monitor TOFU Novos — [Cliente]" que roda diariamente
às 9h. Verificar ads ativos criados nos últimos 7 dias em campanhas com "TOFU"
ou "IMP" no nome. Pausar se gastou mais de R$ 50 e o custo por seguidor está
acima de R$ 4.
```

Threshold de CPF para pause na fase de aprendizado: ~2× o CPF saudável da conta histórica.

---

## Combinação com a skill `cortar-anuncios-sem-conversa`

As duas automações trabalham juntas, cobrindo ciclos diferentes:

```
Dia 0–7  → Monitor de Novos Criativos (esta skill, threshold permissivo)
Dia 8+   → Cortar Anúncios Sem Conversa (skill madura, threshold mais rígido)
```

Não há conflito porque os filtros se complementam: esta skill filtra `ad_created_within: 7 days`, a outra não tem esse filtro mas atinge anúncios maduros porque os novos já foram tratados aqui.

---

## Considerações de compliance

Pause é seguro. Não escala criativo problemático.

---

## Por que dar 7 dias de fair shot e não 3 (como o original)

O guia original (e-commerce) usa 3 dias porque produto físico tem ciclo de decisão curto (visualizar → comprar). Em saúde:

- Paciente vê o anúncio várias vezes antes de iniciar conversa (múltiplos pontos de contato).
- Decisão envolve consulta de informação adicional (Google, Doctoralia, indicação).
- Algoritmo do Meta precisa de mais sinais para identificar paciente certo.

Com 3 dias, você corta criativos que ainda estavam na fase de "assimilação" e teriam performado bem na semana 2. Com 7 dias, dá tempo do funil completar mas ainda corta antes de virar buraco grande.

---

## Quando NÃO usar

- Em campanhas de teste curtas (orçamento limitado, janela de 3 dias intencional) — substituir por monitor manual.
- Em criativos que **dependem de evento específico** (ex.: "Outubro Rosa", "Setembro Amarelo") — janela de aprendizado pode ser maior que o evento, não faz sentido aplicar lógica de 7 dias.

