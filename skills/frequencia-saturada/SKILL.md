name: frequencia-saturada
description: Pausa anúncios Meta Ads onde a frequência de exposição ultrapassou o teto saudável (3,0 prospecting / 4,5 retargeting). Frequência alta indica que a mesma pessoa está sendo bombardeada — em saúde, isso quebra confiança. Roda diariamente. Triggers - "criar automação frequency cap", "frequência saturada", "pausar anúncio frequência alta", "frequência meta ads", "audiência cansada".
---

# Skill: Frequência Saturada (Frequency Cap — Versão Saúde)

Pausa anúncios em que a mesma pessoa já viu o ad muitas vezes. Frequência acima de 3,0 (prospecting) ou 4,5 (retargeting) geralmente significa que você está irritando sua audiência em vez de convertê-la — e em saúde, isso é especialmente crítico porque paciente cansado de ver o mesmo anúncio associa o profissional a "marketing agressivo", o que destrói a percepção de autoridade.

---

## Quando usar

Em todas as contas, sem exceção. Especialmente importante em:
- Campanhas de retargeting com audiências pequenas (< 30.000 pessoas)
- Campanhas evergreen rodando há mais de 30 dias
- Públicos lookalike de bases pequenas (< 1.000 leads de origem)

---

## Configuração por etapa de funil

**Diferente das outras skills, aqui o threshold varia por TIPO de campanha, não por especialidade.** Frequência é métrica universal.

| Tipo de campanha | Frequência ideal | Frequência alerta | Pause |
|---|---|---|---|
| TOFU (prospecting amplo) | 1,0 — 2,0 | > 2,5 | > 3,0 |
| MOFU (engajamento/consideração) | 1,5 — 2,5 | > 3,0 | > 3,5 |
| BOFU (conversão/retargeting) | 2,0 — 3,5 | > 4,0 | > 4,5 |
| Awareness puro (alcance) | 1,0 — 1,8 | > 2,2 | > 2,8 |

Use as colunas "Pause" como threshold da automação. Os valores "alerta" servem para criar uma segunda automação tipo NOTIFY — gestor sabe que o anúncio está chegando perto do limite e pode preparar substituição antes do corte.

---

## Prompt para criar a automação

### Versão consolidada (uma automação por cliente, threshold 3,0)

Para clientes sem estrutura de funil clara ou contas pequenas:

```
Crie uma automação chamada "Frequência Saturada — [Cliente]" que roda diariamente
às 9h. Pausar qualquer anúncio ativo com frequência acima de 3,0 nos últimos 7 dias
que tem pelo menos 1.000 impressões.
```

### Versão por etapa (recomendada para clientes com funil)

Criar 3 automações separadas, filtradas por nome de campanha:

```
Crie uma automação chamada "Frequência TOFU — [Cliente]" que roda diariamente
às 9h. Verificar ads ativos em campanhas com "TOFU" ou "IMP" no nome. Pausar
qualquer anúncio com frequência acima de 3,0 nos últimos 7 dias com pelo menos
1.000 impressões.
```

```
Crie uma automação chamada "Frequência MOFU — [Cliente]" que roda diariamente
às 9h. Verificar ads ativos em campanhas com "MOFU" ou "TRAF" no nome. Pausar
qualquer anúncio com frequência acima de 3,5 nos últimos 7 dias com pelo menos
1.000 impressões.
```

```
Crie uma automação chamada "Frequência BOFU — [Cliente]" que roda diariamente
às 9h. Verificar ads ativos em campanhas com "BOFU" ou "CONV" no nome. Pausar
qualquer anúncio com frequência acima de 4,5 nos últimos 7 dias com pelo menos
1.000 impressões.
```

### Versão NOTIFY antecipado (alerta antes do corte)

Roda em paralelo com as automações de pause. Avisa quando frequência se aproxima do teto, dando tempo de preparar substituição:

```
Crie uma automação chamada "Alerta Frequência — [Cliente]" que roda diariamente
às 9h. Notificar sobre qualquer anúncio ativo com frequência entre 2,5 e 3,0 nos
últimos 7 dias com pelo menos 1.000 impressões.
```

---

## Configuração técnica resultante (versão BOFU)

| Trigger | Performance Threshold (service: meta-ads) |
|---|---|
| **Conditions** | frequency > 4.5 AND impressions >= 1000 (logic: AND) |
| **Lookback** | 7 dias |
| **Aggregation** | Per ad |
| **Filter** | Campaign name contains "BOFU" or "CONV" |
| **Action** | Pause |
| **Frequency** | Daily às 9h |

---

## Por que frequência alta é especialmente crítica em saúde

Em e-commerce, ver o mesmo anúncio 5 vezes é irritante mas não tem consequência. Em saúde, tem 3 efeitos negativos cumulativos:

1. **Erosão de autoridade percebida.** Profissional de saúde que aparece "demais" é percebido como prestador de serviço genérico, não autoridade — exatamente o oposto do que queremos comunicar.

2. **Sinalização de "desespero por pacientes".** Inconsciente do paciente lê alta frequência como "esse médico precisa de pacientes", o que reduz percepção de qualidade. Médico bom é cheio de paciente, não corre atrás.

3. **Risco regulatório.** O CFM tem orientações sobre "publicidade desproporcional" — não é proibição direta, mas em casos extremos pode entrar como infração ética. Frequência muito alta + criativo emocional + público vulnerável (oncologia, infertilidade) é vetor de denúncia.

---

## Considerações de compliance

Pausar por frequência é seguro. A automação reduz risco regulatório, não cria.

---

## O que fazer quando a automação pausar

Pausa por frequência geralmente significa uma destas situações:

1. **Audiência saturada:** o público-alvo já viu tudo que tinha pra ver. Solução: ampliar segmentação (interesses adjacentes, lookalike maior) ou criar nova audiência.

2. **Lote de criativos muito pequeno:** rodando 2 ou 3 criativos só. Solução: subir 4 a 6 criativos novos (a skill `testar-darkposts` ajuda).

3. **Criativos antigos em campanha evergreen:** simplesmente é hora de renovar. Solução: substituir criativos com mais de 30 dias de veiculação.

Após corrigir a causa, reativar manualmente o anúncio (ou subir versão nova) e a automação volta a monitorar.