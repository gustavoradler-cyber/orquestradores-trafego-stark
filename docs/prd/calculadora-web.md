# Calculadora Web — Product Requirements Document (PRD)

**Produto:** Calculadora Web  
**Versão:** 1.0.0  
**Autor:** Morgan (@pm)  
**Data:** 2026-05-08  
**Status:** Draft  
**Baseado em:** Project Brief v1.0 — Atlas (@analyst)

---

## Change Log

| Data | Versão | Descrição | Autor |
|------|--------|-----------|-------|
| 2026-05-08 | 1.0.0 | Criação inicial do PRD | Morgan (@pm) |

---

## 1. Goals and Background Context

### Goals

- Entregar uma calculadora web de página única com as 4 operações básicas (+, -, ×, ÷) funcionando sem erros
- Oferecer display duplo (expressão + resultado) para contexto visual claro durante os cálculos
- Persistir histórico de operações da sessão atual em painel lateral com capacidade de restaurar cálculos anteriores
- Implementar toggle de modo escuro/claro com preferência persistida via `localStorage`
- Superar a percepção de qualidade de calculadoras genéricas com design premium (glassmorphism, micro-animações)
- Entregar solução autocontida (HTML/CSS/JS) sem dependências externas nem servidor

### Background Context

Calculadoras web disponíveis publicamente entregam UX datada: exibem apenas o resultado imediato, não mostram a expressão sendo construída e carecem de histórico visível na tela. O usuário que quer revisar o que calculou há pouco precisa redigitar ou recorrer à memória.

Este produto resolve o problema entregando uma ferramenta de uso imediato no browser — sem instalação, sem conta, sem servidor — com interface premium que diferencia a experiência. O foco do MVP é operações básicas com histórico de sessão, posicionando a calculadora como uma ferramenta confiável e agradável para cálculos rápidos no dia a dia.

---

## 2. Requirements

### Functional Requirements

- **FR1:** A calculadora deve executar as 4 operações aritméticas básicas: adição (+), subtração (-), multiplicação (×) e divisão (÷), com resultados numericamente corretos.
- **FR2:** O display deve ser duplo: linha superior exibe a expressão completa em construção (ex: `12 × 4 =`); linha inferior exibe o resultado em destaque.
- **FR3:** O painel lateral de histórico deve registrar cada operação concluída e exibir no mínimo as últimas 10 operações na sessão atual.
- **FR4:** Clicar em um item do histórico deve restaurar aquela operação no display (expressão + resultado).
- **FR5:** Um botão "Limpar histórico" deve remover todas as entradas do painel de histórico.
- **FR6:** A divisão por zero deve exibir uma mensagem de erro amigável no display, sem travar ou corromper o estado da calculadora.
- **FR7:** A calculadora deve tratar inputs inválidos graciosamente, sem crashes ou comportamento indefinido.
- **FR8:** O toggle de tema (escuro/claro) deve alternar a paleta visual imediatamente e persistir a preferência via `localStorage`, sobrevivendo a recarregamentos da página.
- **FR9:** O modo escuro deve ser o padrão na primeira visita (sem preferência armazenada).

### Non-Functional Requirements

- **NFR1:** Toda a aplicação deve ser autocontida em arquivos estáticos (HTML, CSS, JS) abrível diretamente no browser sem servidor web.
- **NFR2:** Sem dependências externas de runtime: zero frameworks JS, zero bibliotecas CSS de terceiros. Fonte via Google Fonts é permitida.
- **NFR3:** O layout deve ser responsivo (desktop-first), funcional e utilizável em viewports mobile (mínimo 375px de largura).
- **NFR4:** Micro-animações em clique de botões e transição de histórico devem ser suaves (60fps target) e não bloquear interação.
- **NFR5:** O histórico de sessão reside exclusivamente em memória (JS); não persiste entre sessões (por design do MVP).
- **NFR6:** Código JavaScript deve usar ES6+ com separação clara de responsabilidades (estado, DOM, lógica de cálculo).
- **NFR7:** A primeira impressão visual deve ser impactante — design premium é critério de aceite subjetivo revisado pelo autor.

---

## 3. User Interface Design Goals

### Overall UX Vision

Calculadora moderna e sofisticada que equilibra estética premium com usabilidade imediata. O usuário deve sentir que está usando uma ferramenta de qualidade superior no primeiro acesso, sem curva de aprendizado. A interface comunica confiança: display claro, botões responsivos, histórico acessível.

### Key Interaction Paradigms

- **Click/tap direto:** Cada botão responde visualmente ao toque com micro-animação de press
- **Display reativo:** A expressão se constrói visualmente enquanto o usuário digita, dando feedback imediato
- **Histórico contextual:** O painel lateral está sempre visível (desktop) ou acessível, não exige ação extra para consultar
- **Restauração por clique:** Clicar no histórico é a única ação necessária para retomar um cálculo anterior

### Core Screens and Views

- **Tela Principal (única view):** Display duplo + teclado numérico (esquerda) | Painel de histórico (direita)
- **Estado de erro:** Display inferior exibe `Erro: Divisão por zero` ou equivalente, botão C limpa o estado
- **Estado vazio do histórico:** Painel exibe texto placeholder (`Nenhuma operação ainda`)

### Accessibility

WCAG AA básico: contraste mínimo 4.5:1 em texto normal, foco visível em elementos interativos, rótulos semânticos nos botões.

### Branding

- **Modo escuro (padrão):** Fundo azul-violeta escuro (`#0f0f1a` / `#1a1a2e`), acentos em ciano (`#00d4ff`) e verde-limão sutil
- **Modo claro:** Fundo cinza-claro (`#f0f2f5`), cards brancos com sombra suave, acentos em azul-violeta (`#6c63ff`)
- **Glassmorphism:** Cards com `backdrop-filter: blur()` + `background: rgba(255,255,255,0.05)` no dark mode
- **Tipografia:** Inter ou Outfit (Google Fonts) — peso 300/400/700
- **Botões:** Cantos arredondados (`border-radius: 12px`), sombra sutil, estado hover com elevação

### Target Platforms

Web Responsive — desktop-first, funcional em mobile (375px+).

---

## 4. Technical Assumptions

### Repository Structure

Single-directory (sem monorepo). Estrutura flat:
```
calculadora-web/
├── index.html
├── style.css
└── script.js
```

### Service Architecture

Aplicação estática client-side. Zero backend, zero servidor. Toda lógica roda no browser. Deploy = servir arquivos estáticos (ou abrir `index.html` direto).

### Testing Requirements

Sem suite de testes automatizados no MVP. Validação manual pelos critérios de aceite documentados. Lógica de cálculo deve ser isolada em função pura para facilitar testes futuros.

### Additional Technical Assumptions

- Persistência: exclusivamente `localStorage` para tema; histórico em variável JS em memória
- Sem build step, sem transpilação, sem bundler — ES6 modules nativos ou script único
- Tipografia via `<link>` para Google Fonts (Inter ou Outfit)
- CSS Custom Properties para toda a paleta, facilitando o toggle de tema via classe no `<html>`
- JavaScript estruturado em módulo único com separação de responsabilidades por funções nomeadas

---

## 5. Epic List

| # | Epic | Objetivo |
|---|------|----------|
| 1 | Fundação + Calculadora Funcional | Setup do projeto, estrutura HTML semântica, lógica de cálculo completa e display duplo operacional |
| 2 | Histórico de Operações | Painel lateral com registro, exibição e restauração de operações, com botão de limpeza |
| 3 | Design Premium + Tema | Estilos glassmorphism, micro-animações, toggle dark/light com persistência, responsividade mobile |

---

## 6. Epic Details

### Epic 1: Fundação + Calculadora Funcional

**Objetivo:** Estabelecer a estrutura do projeto com HTML semântico e CSS base, implementar o motor de cálculo com as 4 operações, e entregar o display duplo (expressão + resultado) totalmente funcional. Ao final deste epic, a calculadora já é utilizável para seu propósito primário.

---

#### Story 1.1 — Estrutura HTML e Layout Base

**Como** usuário,  
**quero** ver a calculadora com display e teclado numérico dispostos corretamente na tela,  
**para que** eu possa orientar minha interação antes mesmo de clicar em algo.

**Acceptance Criteria:**

1. O arquivo `index.html` existe na raiz com estrutura HTML5 semântica válida
2. O `<head>` inclui `<link>` para Google Fonts (Inter ou Outfit) e referências ao `style.css` e `script.js`
3. O layout apresenta dois blocos lado a lado: bloco da calculadora (display + teclado) à esquerda e bloco de histórico à direita
4. O bloco da calculadora contém: área de display superior (expressão) e área de display inferior (resultado), e grid de botões com: dígitos 0-9, operadores (+, -, ×, ÷), igual (=), ponto decimal (.), limpar (C) e apagar (⌫)
5. O bloco de histórico contém: título "Histórico", lista vazia com placeholder e botão "Limpar histórico"
6. A página abre no browser sem erros no console (zero erros JavaScript, zero recursos 404)
7. A paleta de cores base e CSS Custom Properties estão definidas em `style.css` para dark mode (padrão) e light mode

---

#### Story 1.2 — Motor de Cálculo e Display Duplo

**Como** usuário,  
**quero** realizar cálculos com as 4 operações básicas e ver a expressão construída no display superior e o resultado no display inferior,  
**para que** eu tenha contexto visual completo durante e após cada operação.

**Acceptance Criteria:**

1. Clicar em dígitos atualiza o display inferior com o número sendo inserido
2. Clicar em um operador (+, -, ×, ÷) move o número atual para o display superior como início da expressão (ex: `12 ×`) e limpa o display inferior para novo input
3. Clicar em `=` completa a expressão no display superior (ex: `12 × 4 =`), calcula o resultado e exibe no display inferior
4. A soma, subtração, multiplicação e divisão retornam resultados numericamente corretos (testado com pelo menos 3 operações por tipo)
5. Divisão por zero exibe `Erro: Divisão por zero` no display inferior e `÷ 0 =` no superior; o botão C retorna ao estado limpo
6. O botão C (Clear) limpa ambos os displays e reinicia o estado interno
7. O botão ⌫ (Backspace) remove o último dígito do input atual no display inferior
8. O ponto decimal (`.`) pode ser inserido uma única vez por número; cliques adicionais são ignorados
9. O estado interno da calculadora é gerenciado por funções puras e separadas da manipulação do DOM

---

### Epic 2: Histórico de Operações

**Objetivo:** Implementar o painel lateral de histórico completo: registro automático a cada operação concluída, exibição das últimas N operações, restauração ao clicar em item e limpeza via botão.

---

#### Story 2.1 — Registro e Exibição do Histórico

**Como** usuário,  
**quero** que cada operação que eu concluir (pressionando =) seja automaticamente registrada no painel de histórico,  
**para que** eu possa revisar o que calculei sem precisar redigitar.

**Acceptance Criteria:**

1. Ao pressionar `=`, a operação concluída (expressão + resultado, ex: `12 × 4 = 48`) é adicionada ao topo da lista de histórico
2. O histórico exibe no mínimo as últimas 10 operações na sessão
3. O item mais recente aparece no topo da lista
4. Enquanto não há operações, o painel exibe placeholder (`Nenhuma operação ainda`)
5. Operações com erro (divisão por zero) NÃO são registradas no histórico
6. A lista de histórico tem scroll próprio se o número de itens ultrapassar a altura do painel

---

#### Story 2.2 — Restauração e Limpeza do Histórico

**Como** usuário,  
**quero** clicar em um item do histórico para restaurá-lo no display e limpar o histórico com um botão,  
**para que** eu possa retomar cálculos anteriores e manter o painel organizado.

**Acceptance Criteria:**

1. Clicar em um item do histórico popula o display superior com a expressão e o display inferior com o resultado daquela operação
2. Após restaurar um item, o usuário pode continuar a partir do resultado (pressionando um operador)
3. O botão "Limpar histórico" remove todas as entradas da lista e exibe o placeholder novamente
4. O botão "Limpar histórico" não afeta o estado atual do display (cálculo em andamento não é perdido)
5. O histórico existe apenas em memória da sessão — ao recarregar a página, o histórico começa vazio

---

### Epic 3: Design Premium + Tema

**Objetivo:** Aplicar o design visual completo com glassmorphism, micro-animações e paleta azul-violeta/ciano; implementar o toggle dark/light com persistência via localStorage; garantir responsividade para mobile.

---

#### Story 3.1 — Estilização Premium e Micro-animações

**Como** usuário,  
**quero** ver uma interface com design sofisticado (glassmorphism, gradientes, animações suaves) ao interagir com a calculadora,  
**para que** a experiência de uso seja visualmente diferenciada e agradável.

**Acceptance Criteria:**

1. O fundo da página usa gradiente azul-violeta escuro (`#0f0f1a` → `#1a1a2e` ou equivalente)
2. Os cards (calculadora e histórico) aplicam glassmorphism: `backdrop-filter: blur()` + `background: rgba(255,255,255,0.05)` com borda sutil translúcida
3. Botões numéricos e de operação têm `border-radius: 12px`, sombra suave e estado hover com elevação (`transform: translateY(-2px)`) ou efeito de brilho
4. Clicar em qualquer botão dispara micro-animação de press (ex: `transform: scale(0.95)` + transição 100ms)
5. Novos itens adicionados ao histórico entram com transição suave (fade-in ou slide)
6. A tipografia usa Inter ou Outfit (Google Fonts) — peso light/regular no display superior, bold no resultado
7. O contraste texto/fundo atende WCAG AA (mínimo 4.5:1 para texto normal)

---

#### Story 3.2 — Toggle Dark/Light e Responsividade

**Como** usuário,  
**quero** alternar entre modo escuro e claro com um botão, e usar a calculadora no celular,  
**para que** eu possa adaptar a interface ao meu ambiente e dispositivo.

**Acceptance Criteria:**

1. Um botão/ícone de toggle de tema está visível no header ou canto superior da calculadora
2. Clicar no toggle alterna imediatamente entre dark mode e light mode via troca de classe no elemento `<html>` (ex: `class="light"`)
3. Light mode aplica: fundo cinza-claro (`#f0f2f5`), cards brancos com sombra, acentos em azul-violeta (`#6c63ff`), texto escuro
4. A preferência de tema é salva em `localStorage` (chave `calculadora-tema`) e aplicada ao carregar a página antes de qualquer render visível (sem flash de tema errado)
5. Em viewport de 375px de largura, o layout muda para coluna única: calculadora em cima, histórico embaixo
6. Em mobile, todos os botões do teclado são acessíveis e clicáveis sem zoom (tamanho mínimo 44×44px por botão)
7. O painel de histórico em mobile tem altura máxima definida com scroll próprio

---

## 7. Checklist Results

*A ser executado com `@po *validate-story-draft` antes da implementação.*

| Item | Status |
|------|--------|
| Todas as FRs rastreáveis a pelo menos uma story | Pendente |
| Todas as NFRs incorporadas em ACs ou stories | Pendente |
| Stories independentes dentro de cada epic | Pendente |
| Stories dimensionadas para sessão única de agente | Pendente |
| Critérios de aceite testáveis e não ambíguos | Pendente |
| Epic 1 entrega valor standalone | Pendente |
| Sequência de epics é logicamente correta | Pendente |

---

## 8. Next Steps

### UX Expert Prompt

> `@ux-design-expert` — Com base no PRD `docs/prd/calculadora-web.md`, crie o wireframe detalhado e o design spec da Calculadora Web. Foco em: (1) layout desktop e mobile com medidas, (2) tokens de cor completos para dark/light mode, (3) especificações de glassmorphism e animações. Use o `*create-design-spec` para gerar o artefato.

### Architect Prompt

> `@architect` — Com base no PRD `docs/prd/calculadora-web.md`, avalie a arquitetura técnica da Calculadora Web. É uma SPA estática sem backend. Confirme: (1) estrutura de arquivos recomendada, (2) padrão de organização do JavaScript (módulo único vs múltiplos), (3) estratégia de CSS Custom Properties para temas. Use `*create-architecture` para gerar o artefato.

---

*PRD gerado por Morgan (@pm) — AIOX Framework v2.0*
