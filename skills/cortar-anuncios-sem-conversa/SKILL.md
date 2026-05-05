name: cortar-anuncios-sem-conversa
description: Pausa automaticamente anúncios Meta Ads que gastaram verba acima do limiar mas não geraram nenhuma conversa iniciada (WhatsApp/DM) no período. Roda diariamente. Foco saúde — calibrado por especialidade. Triggers - "criar automação de kill switch", "cortar anúncios sem conversa", "pausar anúncio sem lead", "automação de corte de anúncios", "ad sem conversa pausa".
---

# Skill: Cortar Anúncios Sem Conversa (Kill Switch)

Rede de proteção mais básica e mais importante. Pausa qualquer anúncio que gastou verba real mas não gerou nenhuma conversa iniciada (mensagens no WhatsApp/DM via `actions_onsite_conversion.messaging_conversation_started_7d`). Roda todo dia para que nada sangre verba durante a noite ou fim de semana.

**Esta deve ser a PRIMEIRA automação a ativar em qualquer conta nova da Stark.**

---

## Quando usar

Sempre. Para todos os 11 clientes. Não tem exceção.

A única calibração necessária é o threshold de gasto, que varia por especialidade — porque o que é "verba significativa" para uma campanha de cirurgia plástica (CPL R$ 6–15) é diferente do que é significativo para oncologia (CPL R$ 40–100).

---

## Configuração por especialidade

Tabela de threshold de gasto sem conversa para acionar pause (lookback 3 dias):

| Especialidade | Spend mínimo para pausar | CPL benchmark |
|---|---|---|
| Cirurgia Plástica | R$ 30 | R$ 6 — R$ 15 |
| Cirurgia Plástica (face) | R$ 36 | R$ 8 — R$ 18 |
| Cirurgia Ortognática | R$ 80 | R$ 20 — R$ 50 |
| Dermatologia | R$ 50 | R$ 10 — R$ 25 |
| Medicina Estética | R$ 90 | R$ 20 — R$ 60 |
| Tricologia | R$ 60 | R$ 15 — R$ 35 |
| Implantes Dentários | R$ 90 | R$ 25 — R$ 55 |
| Emagrecimento | R$ 100 | R$ 25 — R$ 80 |
| Oncologia | R$ 150 | R$ 40 — R$ 100 |
| Cirurgia Cabeça e Pescoço | R$ 120 | R$ 35 — R$ 90 |
| Anestesia | R$ 120 | R$ 30 — R$ 80 |
| Médico de Família | R$ 50 | R$ 10 — R$ 30 |
| Saúde Geral | R$ 60 | R$ 15 — R$ 40 |

**Regra:** spend mínimo = ~3× o piso do CPL benchmark. Se em 3 dias gastou 3× o que custaria 1 lead saudável e não trouxe nenhuma conversa, é desperdício comprovado.

---

## Prompt para criar a automação

Adaptar o nome da campanha e o threshold ao cliente. Modelo genérico:

```
Crie uma automação chamada "Kill Switch — [Nome do Cliente]" que roda diariamente
às 9h. Verificar todos os anúncios ativos da conta [account_id] que gastaram mais
de R$ [X] nos últimos 3 dias. Se o anúncio teve 0 conversas iniciadas
(messaging_conversation_started_7d), pausar. Verificar em todas as campanhas.
```

Exemplo concreto — Dr. Marcus Calazans (Cirurgia Plástica):

```
Crie uma automação chamada "Kill Switch — Marcus Calazans" que roda diariamente
às 9h. Verificar todos os anúncios ativos que gastaram mais de R$ 30 nos últimos
3 dias. Se o anúncio teve 0 conversas iniciadas, pausar.
```

---

## Configuração técnica resultante

| Trigger | Performance Threshold (service: meta-ads) |
|---|---|
| **Conditions** | spend > [valor] AND messaging_conversation_started_7d = 0 (logic: AND) |
| **Lookback** | 3 dias |
| **Aggregation** | Per ad |
| **Action** | Pause |
| **Frequency** | Daily às 9h |

---

## Considerações de compliance

Auto-pause é a ação mais segura do ponto de vista regulatório. Não há risco de violação de CFM/CRM/CRO/COREN ao pausar — só ao publicar/escalar. Esta automação SOMENTE pausa, então pode rodar em modo totalmente automático sem revisão humana.

---

## Variantes recomendadas

Para clientes com estrutura de funil clara (TOFU/MOFU/BOFU), criar uma automação separada filtrando por nome de campanha — porque o threshold para pausar muda por etapa:

```
Crie uma automação "Kill Switch BOFU — [Cliente]" que roda diariamente. Verificar
ads ativos em campanhas com "BOFU" ou "CONV" no nome. Pausar se gastou mais de
R$ [X] nos últimos 3 dias e teve 0 conversas iniciadas.
```

Para TOFU (tráfego de seguidores/alcance), o critério muda — não é "0 conversas", é "0 novos seguidores" ou "CPF acima de R$ 3":

```
Crie uma automação "Kill Switch TOFU — [Cliente]" que roda diariamente. Verificar
ads ativos em campanhas com "TOFU" ou "IMP" no nome. Pausar se gastou mais de
R$ [X] nos últimos 3 dias e o custo por seguidor está acima de R$ 3.
```

---

## Quando NÃO usar

- Primeiros 3 dias de aprendizado de uma campanha nova — o algoritmo do Meta ainda está calibrando. Use a skill `monitor-novos-criativos` para esses casos, com threshold mais permissivo.
- Campanhas de **awareness puro** sem objetivo de conversa — a métrica de conversa não se aplica. Para essas, calibrar pelo CPM ou alcance.