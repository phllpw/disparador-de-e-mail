---
name: Disparador de E-mails IPNET By Vivo
description: Enterprise Workspace Console para envios operacionais em lote no Google Workspace
colors:
  primary: "#4f46e5"
  primary-hover: "#4338ca"
  primary-focus: "#6366f1"
  neutral-bg: "#f8fafc"
  neutral-card: "#ffffff"
  neutral-subsurface: "#f1f5f9"
  neutral-border: "#e2e8f0"
  text-main: "#0f172a"
  text-muted: "#64748b"
  text-subtle: "#94a3b8"
  dark-bg: "#090d16"
  dark-card: "#0f172a"
  dark-subsurface: "#0b1120"
  dark-border: "rgba(255, 255, 255, 0.08)"
  dark-text-main: "#f8fafc"
  dark-text-muted: "#94a3b8"
  dark-text-subtle: "#64748b"
  status-success: "#10b981"
  status-warning: "#f59e0b"
  status-error: "#ef4444"
typography:
  display:
    fontFamily: "Inter, -apple-system, BlinkMacSystemFont, sans-serif"
    fontSize: "16px"
    fontWeight: 600
    lineHeight: 1.25
    letterSpacing: "-0.02em"
  headline:
    fontFamily: "Inter, -apple-system, BlinkMacSystemFont, sans-serif"
    fontSize: "14px"
    fontWeight: 600
    lineHeight: 1.35
    letterSpacing: "-0.01em"
  title:
    fontFamily: "Inter, -apple-system, BlinkMacSystemFont, sans-serif"
    fontSize: "13px"
    fontWeight: 600
    lineHeight: 1.4
    letterSpacing: "normal"
  body:
    fontFamily: "Inter, -apple-system, BlinkMacSystemFont, sans-serif"
    fontSize: "13px"
    fontWeight: 400
    lineHeight: 1.5
    letterSpacing: "normal"
  label:
    fontFamily: "Inter, -apple-system, BlinkMacSystemFont, sans-serif"
    fontSize: "12px"
    fontWeight: 500
    lineHeight: 1.4
    letterSpacing: "normal"
  microcopy:
    fontFamily: "Inter, -apple-system, BlinkMacSystemFont, sans-serif"
    fontSize: "11px"
    fontWeight: 500
    lineHeight: 1.4
    letterSpacing: "normal"
  mono:
    fontFamily: "JetBrains Mono, monospace"
    fontSize: "11px"
    fontWeight: 500
    lineHeight: 1.4
    letterSpacing: "normal"
rounded:
  sm: "6px"
  md: "8px"
  lg: "12px"
  full: "9999px"
spacing:
  xs: "4px"
  sm: "8px"
  md: "12px"
  lg: "16px"
  xl: "20px"
components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "#ffffff"
    rounded: "{rounded.md}"
    padding: "8px 16px"
  button-primary-hover:
    backgroundColor: "{colors.primary-hover}"
  button-secondary:
    backgroundColor: "{colors.neutral-card}"
    textColor: "{colors.text-main}"
    rounded: "{rounded.md}"
    padding: "7px 12px"
  input-field:
    backgroundColor: "{colors.neutral-bg}"
    textColor: "{colors.text-main}"
    rounded: "{rounded.md}"
    padding: "6px 10px"
  card-container:
    backgroundColor: "{colors.neutral-card}"
    rounded: "{rounded.lg}"
    padding: "16px"
---

# Design System: Disparador de E-mails IPNET By Vivo

## Overview

**Creative North Star: "The Enterprise Workspace Console"**

O Disparador de E-mails IPNET By Vivo é uma ferramenta operacional interna de alta precisão e densidade calibrada para o Google Workspace. Não é uma landing page, não é um dashboard decorativo e não é um SaaS de marketing.

**Hierarquia de Prioridades:**
$$\text{Velocidade Operacional} > \text{Clareza} > \text{Seguran\c{c}a} > \text{Est\'{e}tica}$$

A estética está estritamente subordinada ao fluxo de trabalho do operador comercial e de Customer Success da IPNET By Vivo. O objetivo primário é compor com rapidez, importar contatos com segurança, validar antes de qualquer envio e garantir entrega previsível sem riscos à cota do Workspace ou à reputação do domínio.

**Princípios Visuais:**
- **Superfícies Predominantemente Planas**: Estrutura *flat-at-rest*. Separação nítida obtida por variações tonais sutis de superfície e alinhamento geométrico, nunca por elevações falsas.
- **Bordas Hairline de 1px**: Linhas de 1px aplicadas exclusivamente onde ajudam a delimitar estrutura e fluxo (contorno de painéis principais, divisores de seção e tabelas). Sem sobreposição desnecessária de bordas.
- **Zero Sombras no Workspace**: Praticamente nenhuma sombra no plano de trabalho; sombras são reservadas estritamente a overlays e modais flutuantes com desfoque de fundo.
- **Sem Vícios de AI Vibe Coding**: Sem gradientes decorativos, sem efeitos de glow, sem glassmorphism, sem bento grid ornamental, sem abordagem icon-heavy e **sem cards dentro de cards**.

## Colors

Paleta neutra sóbria com uso estritamente semântico e parcimonioso de acentos operacionais.

### Primary (Indigo Functional)
- **Indigo Core** (`#4f46e5` em Light / `#6366f1` em Dark): Reservado estritamente à ação principal determinante (**Revisar e Disparar Lote**, **Substituir Tabela**), anéis de foco ativo em inputs e estados selecionados/ativos da barra de formatação.
- **Indigo Hover** (`#4338ca` em Light / `#4f46e5` em Dark): Resposta tátil de clique.
- **Regra de Cobertura:** O tom Índigo deve cobrir $\le 5\%$ da superfície total do viewport, garantindo gravidade instantânea ao botão de envio.

### Neutros e Superfícies
- **App Canvas** (`#f8fafc` em Light / `#090d16` em Dark): Plano de fundo neutro do console.
- **Surface Card** (`#ffffff` em Light / `#0f172a` em Dark): Painéis operacionais principais.
- **Sub-Surface Inset** (`#f1f5f9` em Light / `#0b1120` em Dark): Fundo de inputs, células em repouso e cabeçalhos de tabela.
- **Structural Border** (`#e2e8f0` em Light / `rgba(255, 255, 255, 0.08)` em Dark): Delimitação sutil de 1px.
- **Text Main** (`#0f172a` em Light / `#f8fafc` em Dark): Títulos, valores de dados e texto do editor. Contraste $\ge 7:1$.
- **Text Muted** (`#64748b` em Light / `#94a3b8` em Dark): Rótulos de campos e instruções. Contraste $\ge 4.5:1$ (WCAG AA).
- **Text Subtle** (`#94a3b8` em Light / `#64748b` em Dark): Placeholders e contadores monoespaçados.

### Status Accents (Semântica Estrita)
- **Emerald** (`#10b981`): Usado **exclusivamente para sucesso** (conta ativa conectada, salvamento local confirmado, relatório de envio concluído com êxito).
- **Amber** (`#f59e0b`): Usado **exclusivamente para warnings** e avisos de risco operacional (alerta de multiplicação de e-mails em CC/CCO no modal de confirmação e cota diária crítica).
- **Rose / Red** (`#ef4444`): Usado **exclusivamente para erros e ações destrutivas** (célula com e-mail inválido `.invalid-email`, bloqueio de cota insuficiente e botão de exclusão de linha).

## Typography

Pareamento disciplinado entre tipografia humana neutra e monoespaçada técnica.

**UI & Body Font:** `Inter, -apple-system, BlinkMacSystemFont, sans-serif`  
**Technical & Data Font:** `JetBrains Mono, monospace`  

### Escala Tipográfica (Ratio 1.25x Proporcional)
- **Display** (Semibold 600, 16px, line-height 1.25): Título da aplicação no cabeçalho.
- **Headline** (Semibold 600, 14px, line-height 1.35): Títulos dos painéis principais (Envelope & Mensagem / Destinatários).
- **Title** (Semibold 600, 13px, line-height 1.4): Rótulos de campos primários (De, Assunto, Corpo).
- **Body** (Regular 400, 13px, line-height 1.5): Texto do editor rico, prévia e dados nas células da tabela.
- **Label** (Medium 500, 12px, line-height 1.4): Rótulos de apoio e botões secundários.
- **Microcopy** (Medium 500, 11px, line-height 1.4): Dicas de rodapé, notas de ajuda e cabeçalhos de tabela (sem uppercase forçado).
- **Mono Technical** (Medium 500, 11px, line-height 1.4): Tokens dinâmicos (`{{Nome}}`, `{{Empresa}}`, `{{GN}}`), contadores numéricos, endereço da conta ativa e cota disponível.

### Named Rules
- **The Explicit Tag Rule:** Variáveis dinâmicas são renderizadas sempre em `JetBrains Mono` com pílulas delimitadas (`.var-pill` ou `.variable-token`), nunca em texto corrido sem demarcação.
- **No-Fake-Denominator Rule:** Nunca inventar denominadores fictícios para cota (ex: `1.450 / 1.500`). Exibir apenas o valor real fornecido pelo backend: `1.450 disponíveis`.

## Layout & Arquitetura de Informação

### Fluxo Operacional Mental
$$\text{Envelope/Composer} \longrightarrow \text{Destinat\'{a}rios} \longrightarrow \text{Revis\~{a}o (Pr\'{e}via)} \longrightarrow \text{Envio}$$

1. **Envelope/Composer:** O remetente (`Remetente (De)`) pertence conceitualmente ao envelope da mensagem e situa-se no topo da coluna da esquerda, antecedendo o `Assunto` e o `Corpo`.
2. **Destinatários:** Tabela densa e operacional na coluna da direita com controles integrados no topo e opções de envio compactas.
3. **Revisão:** Acesso centralizado e representativo à **Prévia Realista** compilada com os dados reais dos contatos.
4. **Envio:** Área de ação acoplada à base da tabela com resumo de impacto e confirmação segura.

### Distribuição Responsiva
- **Desktop Intermediário (1024px a 1439px):** Divisão simétrica equilibrada **6/6** para garantir largura horizontal confortável para as 4 colunas da tabela de destinatários.
- **Monitores Amplos ($\ge$ 1440px):** Testar distribuição **7/5** favorável ao Composer apenas se a tabela mantiver legibilidade perfeita com a coluna GN habilitada. A legibilidade dos dados prevalece sobre estética geométrica.
- **Tablet (768px a 1023px):** Colapso para coluna única sequencial: Envelope -> Editor -> Destinatários -> Ação.
- **Mobile (< 768px):** Sequência vertical estrita com tabela horizontalmente rolável e botão de disparo full-width.

## Elevation & Depth

- **Resting State (Plano):** `box-shadow: none;`. Superfícies e cartões assentam sobre o canvas com separação por contorno sutil de 1px e leve variação tonal de fundo.
- **Focused State:** Anel sutil de 2px `rgba(99, 102, 241, 0.15)` sem deslocamento vertical.
- **Overlays / Modais Flutuantes:** Sombra `0 20px 35px -10px rgba(0, 0, 0, 0.5)` acompanhada de `backdrop-filter: blur(4px)` para direcionamento cognitivo total à revisão ou importação.

## Shapes & Radii

- **Cards e Modais:** `12px` (`rounded-xl` calibrado).
- **Inputs, Botões e Selects:** `8px` (`rounded-lg` funcional).
- **Pílulas de Variáveis, Badges e Botões de Toolbar:** `6px` (`rounded-md` compacto).
- **Indicadores de Conectividade:** `9999px` (`rounded-full`).

## Components

### 1. Application Header
- Logo corporativo IPNET e badge "By Vivo" discretos sem bordas pesadas.
- Contexto do operador unificado em linha: `phellipe@ipnet.cloud · 1.450 disponíveis`.
- Botão de alternância de tema claro/escuro simples e nítido.

### 2. Envelope & Composer (Coluna Esquerda)
- **Remetente (De):** Dropdown de aliases no topo da composição do envelope.
- **Assunto:** Campo de texto com contador de caracteres monoespaçado (`0 / 250`) e controle compacto de inserção de variáveis (`+ {{Nome}}`, `+ {{Empresa}}`, `+ {{GN}}`).
- **Editor Rico:** Contêiner de edição com toolbar compacta (sem traços divisórios cinzas excessivos) contendo botões de formatação e controle de variáveis próprio.
- **Assinatura do Gmail:** Linha compacta integrada ao rodapé do editor: `● Assinatura Gmail sincronizada · Recarregar`. Sem card separado.

### 3. Destinatários & Tabela (Coluna Direita)
- **Barra de Ferramentas Integrada:** Título da seção, contador de contatos válidos, botões de ação (`Colar Planilha`, `+ Linha`, `Limpar`) e toggle `[x] Coluna GN` em alinhamento único.
- **Opções de Envio (CC e CCO):** Disclosure compacto com link sutil `+ Adicionar CC/CCO`. Ao expandir, exibe campos limpos com descrição explicativa sem moldura pesada.
- **Tabela Densa Operacional:**
  - Sem bordas verticais divisórias nas células.
  - Divisores horizontais sutis de 1px.
  - Células parecem texto em repouso; foco e borda aparecem somente durante a digitação ativa.
  - Cabeçalho sticky com tipografia natural em 11px (`Nome`, `E-mail`, `Empresa`, `GN`).
  - Destaque imediato em vermelho (`.invalid-email`) para e-mails mal formatados.
  - Botão de remoção de linha discreto no hover.

### 4. Action Area (Acoplada à Tabela)
- Ancorada diretamente na base do painel de destinatários.
- Resumo em linha: `N destinatários válidos · consumo estimado da cota: X`.
- Botão secundário: `Prévia Realista` (ação única de prévia completa).
- Botão primário dominante: `Revisar e Disparar Lote` (`btn-primary` com ícone discreto).
- Estado de envio durante RPC: Indeterminado (`Enviando...`) desabilitando os botões sem inventar percentuais falsos.

### 5. Modais
- **Prévia Realista:** Simulação visual autêntica do cliente de e-mail (remetente, destinatário da primeira linha, CC se houver, assunto e corpo com assinatura renderizada). Badge discreto de CCO administrativo.
- **Confirmação de Disparo:** Tabela chave-valor de dados do lote (destinatários, consumo de cota, remetente, CC, CCO, assunto) e **aviso dinâmico em âmbar** calculando o impacto de cópias por mensagem.
- **Importação:** Campo monospace para colagem rápida de dados TSV/CSV com opções claras de substituição ou concatenação.

## Accessibility & Interactions

- **Acessibilidade de Foco:**
  - Focus trap ativo ao abrir qualquer modal.
  - Foco inicial direcionado para o primeiro botão ou campo relevante do modal.
  - Restauração do foco para o botão de origem ao fechar.
  - Fechamento pela tecla `Escape` rigorosamente preservado nos 3 modais.
- **Semântica ARIA:**
  - Editor rico com `role="textbox"`, `aria-multiline="true"` e `aria-label="Corpo da mensagem"`.
  - Inputs da tabela com `aria-label="Nome linha X"`, `aria-label="E-mail linha X"`.
- **Sensibilidade ao Movimento:**
  - Suporte completo a `@media (prefers-reduced-motion: reduce)` neutralizando transições e saltos visuais.
- **Microcopy Contrast:**
  - Rótulos e contadores com razão de contraste $\ge 4.5:1$ em ambos os temas.
- **Touch Areas:**
  - Densidade preservada para mouse/trackpad em desktop.
  - Espaçamentos e áreas de toque ampliadas sob consulta de mídia `@media (pointer: coarse)`.

## Do's and Don'ts

### Do:
- **Do** manter a composição do e-mail em ordem lógica: De -> Assunto -> Corpo.
- **Do** manter a área de ação conectada diretamente à base da tabela de destinatários.
- **Do** exibir a cota diária exatamente como informada pelo backend Google Workspace (`X disponíveis`).
- **Do** usar a cor âmbar exclusivamente para mensagens de aviso prévio e risco operacional de cota/lote.
- **Do** validar e realçar imediatamente e-mails com erro de sintaxe.

### Don't:
- **Don't** criar caixas ou cartões aninhados dentro de outros cartões (*nested cards*).
- **Don't** inventar números totais ou porcentagens de cota que o backend não fornece.
- **Don't** espalhar múltiplos botões de preview redundantes com pesos conflitantes.
- **Don't** usar sombras salientes ou gradientes decorativos no espaço de trabalho.
- **Don't** colocar o campo de Remetente dentro do card de Destinatários.
