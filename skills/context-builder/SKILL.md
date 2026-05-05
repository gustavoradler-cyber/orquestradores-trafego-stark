# WORKFLOW: SETUP INICIAL DO CONTEXTO DE CLIENTES — FULL BUILD

> Prompt auxiliar A — executar 1x por cliente (setup inicial ou onboarding). Atualizações semanais devem usar o Prompt B (Context Updater).

---

## OBJETIVO

Criar a pasta consolidada **"Contexto Clientes - Stark"** no meu Google Drive (raiz) e gerar um Google Doc por cliente, contendo histórico consolidado a partir de duas fontes:

1. Subpasta "Documentos" da pasta de cada cliente em `/Clientes/`.
2. Transcrições de reunião do Gemini Notes recebidas no meu Gmail (remetente `gemini-notes@google.com`, assunto começa com "Anotações:").

---

## CONFIGURAÇÕES FIXAS

- **Pasta consolidada:** "Contexto Clientes - Stark" — criar na raiz do meu Drive.
- **Pasta-mãe dos clientes:** `/Clientes/` no Drive.
- **Subpasta de documentos do cliente:** buscar por substring case-insensitive contendo "documento" (pode haver prefixo numérico do tipo "04. Documentos | Dr X").
- **Remetente das transcrições:** `gemini-notes@google.com`
- **Assunto das transcrições:** começa com "Anotações:" e contém o nome+sobrenome do cliente.
- **Cliente excluído permanentemente:** Dr. Fernando Mattioli - FACE (não criar doc).

---

## CLIENTES (11 — gerar 1 doc para cada)

Localizar pasta no Drive por nome+sobrenome (tolerar variações como "Dr"/"Dr.", e prefixos numéricos):

- Dr. Marcelo Bezerra
- Dra. Nicolli
- Graciela Machado
- Dr. Fernando Bezerra
- Fernanda Encinas
- Dr. Laureano Filho
- Dra. Érica Marchiori
- Dr. Diego Alencar
- Dra. Mariângela Santiago
- Dr. Higner Forastieri
- Dr. Caio Fernandes

---

## PASSOS DE EXECUÇÃO

### PASSO 1 — Criar pasta consolidada

- Verificar se "Contexto Clientes - Stark" já existe na raiz do Drive.
- Se não existir, criar (`mimeType=application/vnd.google-apps.folder`, `parent=root`).
- Guardar o `folder_id` da pasta consolidada para uso nos passos seguintes.

### PASSO 2 — Localizar pastas dos clientes (paralelo, 11 buscas)

- Para cada um dos 11 clientes, buscar dentro de `/Clientes/` a subpasta cujo nome contenha o nome+sobrenome do cliente.
- Se não encontrar a pasta do cliente, registrar warning e seguir adiante (modo email-only para esse cliente).

### PASSO 3 — Listar conteúdo da subpasta "Documentos" (paralelo)

- Para cada pasta de cliente encontrada, buscar a subpasta cujo nome contenha "documento" (case-insensitive).
- Listar arquivos do tipo Google Doc, .docx e .pdf dessa subpasta.
- Se não houver subpasta "Documento(s)", marcar como email-only e prosseguir.

### PASSO 4 — Buscar transcrições do Gemini no Gmail (paralelo)

Para cada cliente, executar busca no Gmail com a query:

```
from:gemini-notes@google.com subject:"Anotações" subject:"{Nome Sobrenome}"
```

- Listar todas as threads encontradas (sem filtro de data — full history).
- Cada email do Gemini tem um botão "Abrir ata da reunião" que aponta para um Google Doc — capturar tanto o resumo do corpo do email quanto o link/conteúdo da ata quando possível.

### PASSO 5 — Ler conteúdo (paralelo, em batches)

- Para cada arquivo do Drive: ler conteúdo textual (pular planilhas comerciais, mídias e arquivos > 200KB).
- Para cada thread/email: extrair corpo + (se acessível) o conteúdo da ata da reunião.
- Capturar resumo executivo, decisões e ações — **NÃO** o transcript bruto inteiro.

### PASSO 6 — Sintetizar contexto por cliente

Para cada cliente, montar o doc "Contexto - {Nome do Cliente}" com a estrutura padrão abaixo. Não inventar conteúdo — se faltar dado, escrever "Sem registros disponíveis até a presente data":

```markdown
# Contexto - {Nome do Cliente}

## Perfil & Especialidade
[Síntese: especialidade médica, localização, perfil de paciente,
 posicionamento de marca. Extrair de briefings, contratos e
 planejamentos. 2-4 parágrafos curtos.]

## Momento Comercial Atual
[Descrição do momento atual: alta/baixa demanda, sazonalidade conhecida,
 metas declaradas (ex: 15 cirurgias/mês), desafios reportados.
 Substituível pelo Prompt B se houver mudança evidente.]

## Histórico de Reuniões
- [DD/MM/AAAA] Título da reunião — pontos discutidos em 1-3 bullets
- [DD/MM/AAAA] ...
[Em ordem cronológica decrescente, sempre datado.]

## Pontos de Atenção Recorrentes
- Tema 1: [descrição curta]
- Tema 2: ...
[Padrões que aparecem em mais de uma reunião.]

## Aprendizados de Tráfego (append-only)
[Esta seção é alimentada pelo Prompt C ao final de cada relatório
 semanal. Inicialmente vazia.]

---
**Última Atualização:** {YYYY-MM-DD}
**Modo da última atualização:** FULL BUILD
```

### PASSO 7 — Criar docs no Drive (paralelo, 11 chamadas)

- Para cada cliente, criar arquivo na pasta consolidada (`parent=folder_id` da Pasta Consolidada).
- Nome do arquivo: `Contexto - {Nome do Cliente}`.
- MimeType: `application/vnd.google-apps.document`.
- Conteúdo: texto sintetizado seguindo a estrutura do Passo 6.

### PASSO 8 — Output final

Reportar tabela com:

- Pasta consolidada — link e status (criada agora / já existia).
- Por cliente: ✅ doc criado | ⚠️ pasta-cliente não encontrada (email-only) | ⚠️ sem subpasta "Documentos" (email-only) | ❌ falha.
- Total de transcrições processadas por cliente.
- Total de documentos do Drive lidos por cliente.

---

## REGRAS

- **Privacidade:** os docs ficam apenas no meu Drive. Não compartilhar.
- **Idempotência:** se um doc "Contexto - {Nome}" já existe na pasta consolidada, perguntar antes de sobrescrever.
- **Sem alucinação:** se não houver dado para uma seção, escrever "Sem registros disponíveis até a presente data" em vez de inventar.
- **Datas:** formato `YYYY-MM-DD` para "Última Atualização"; `DD/MM/AAAA` no histórico.
- **Conteúdo de transcrição:** capturar resumo + decisões + ações — NÃO o transcript bruto.
- **Rate limit do Gmail/Drive:** paralelizar em batches de até 10 chamadas; aguardar antes de continuar caso atinja limite.
