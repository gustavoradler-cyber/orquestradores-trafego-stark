# Project Brief — Calculadora Web

**Versão:** 1.0  
**Data:** 2026-05-08  
**Status:** Aprovado para desenvolvimento  
**Analista:** Atlas (@analyst)

---

## 1. Visão Geral

Uma calculadora web de página única com operações matemáticas básicas e histórico de operações persistente na sessão. Interface premium com suporte a modo escuro e experiência visual diferenciada.

---

## 2. Problema / Oportunidade

Calculadoras web genéricas oferecem UX pobre: sem histórico visível, sem exibição da expressão completa, e design datado. A oportunidade é entregar uma ferramenta funcional com design moderno que eleva a percepção de qualidade.

---

## 3. Objetivos

| # | Objetivo | Critério de Sucesso |
|---|---|---|
| 1 | Operações básicas funcionais | Soma, subtração, multiplicação e divisão sem erros |
| 2 | Histórico de operações | Últimas N operações visíveis em painel lateral |
| 3 | Display duplo | Expressão completa no topo + resultado em destaque |
| 4 | Modo escuro/claro | Toggle funcional com preferência persistida |
| 5 | Design premium | Interface que impressiona no primeiro acesso |

---

## 4. Escopo

### ✅ Incluído (MVP)

- **Operações:** `+`, `-`, `×`, `÷`
- **Display duplo:** linha superior = expressão (`12 × 4 =`), linha inferior = resultado (`48`)
- **Painel lateral de histórico:** lista das últimas operações, clicável para restaurar
- **Modo escuro/claro:** toggle com persistência via `localStorage`
- **Limpeza de histórico:** botão "Limpar histórico"
- **Tratamento de erros:** divisão por zero, inputs inválidos

### ❌ Fora do Escopo (v1)

- Operações avançadas (raiz, potência, trigonometria)
- Exportação do histórico
- Anotações por operação
- Histórico persistente entre sessões

---

## 5. Usuários-Alvo

**Usuário primário:** Qualquer pessoa que precise de cálculos rápidos no browser sem precisar instalar nada.

**Jobs-to-be-done:**
- *"Quero calcular algo rápido sem sair do navegador"*
- *"Quero revisar o que calculei há pouco sem redigitar"*

---

## 6. Stack Técnica

| Camada | Tecnologia |
|---|---|
| Estrutura | HTML5 semântico |
| Estilo | CSS Vanilla (variáveis CSS, glassmorphism, animações) |
| Lógica | JavaScript vanilla (ES6+) |
| Persistência | `localStorage` (tema) |
| Tipografia | Google Fonts (Inter ou Outfit) |

**Sem frameworks, sem dependências externas.**

---

## 7. Design Direction

- **Estética:** Dark mode como padrão, glassmorphism nos cards, gradientes sutis
- **Paleta:** Tons de azul-violeta escuro com acentos em ciano/verde
- **Animações:** Micro-animações em clique de botões, transição suave do histórico
- **Layout:** Display + teclado numérico à esquerda | Painel de histórico à direita

---

## 8. Critérios de Aceite

- [ ] Todas as 4 operações funcionam corretamente
- [ ] Histórico exibe no mínimo as últimas 10 operações
- [ ] Clicar em item do histórico restaura a operação no display
- [ ] Toggle de tema funciona e persiste ao recarregar
- [ ] Divisão por zero exibe mensagem de erro amigável
- [ ] Layout responsivo (desktop-first, funcional em mobile)
- [ ] Primeira impressão visual é impactante (design premium)

---

## 9. Entregável

Um único arquivo `index.html` autocontido (CSS e JS inline ou em arquivos separados na mesma pasta), pronto para abrir no browser sem servidor.

---

*Project Brief gerado por Atlas (@analyst) — AIOX Framework*
