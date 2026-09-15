# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Users

- **Usuário Primário**: Equipes comerciais, Customer Success (CS) e executivos de contas da **IPNET By Vivo**.
- **Cenário de Uso**: Disparos de e-mails em lote personalizados e segmentados para contatos e tomadores de decisão em clientes corporativos (B2B).
- **Trabalho a Ser Feito (Job)**: Importar contatos rapidamente de planilhas (Google Sheets/Excel), customizar mensagem com variáveis contextuais ({{Nome}}, {{Empresa}}, {{GN}}), definir remetente/aliases e disparar de forma segura respeitando cotas diárias e políticas anti-spam do Google Workspace.

## Product Purpose

Disponibilizar uma ferramenta ágil, confiável e nativa para envio de e-mails individuais em lote com personalização dinâmica. O produto existe para eliminar o trabalho manual de envio repetitivo e evitar o overhead de contratar ou configurar plataformas complexas de marketing automation para comunicações comerciais diretas. O sucesso significa envio consistente, alta entregabilidade, controle transparente da cota e zero duplicidade acidental.

## Positioning

Diferencia-se por ser uma solução serverless nativa construída em Google Apps Script diretamente integrada ao Gmail e ao Google Workspace da IPNET By Vivo:
- Utiliza a autoridade de domínio e a reputação da própria conta corporativa ativa (SendAs e assinatura oficial sincronizada).
- Foco primordial em simplicidade, segurança e integração nativa com o Workspace, sem necessidade de servidores externos, mas com flexibilidade arquitetural para suportar automações futuras graduais.

## Operating Context

- **Ambiente de Execução**: Google Apps Script Web App executado no navegador, compatível tanto em execução autenticada na nuvem do Google quanto em modo de teste local.
- **Entrada de Dados**: Colagem direta de dados tabulares (TSV/CSV) oriundos do Google Sheets ou Excel, com detecção inteligente de cabeçalhos e preenchimento manual alternativo.
- **Saída**: Transmissão sequencial de e-mails pelo GmailApp com intervalo de proteção anti-spam (~300ms) e persistência de rascunho em localStorage.

## Capabilities and Constraints

- **Capacidades Confirmadas**:
  - Envio individualizado em lote com tags dinâmicas no assunto e corpo da mensagem ({{Nome}}, {{Empresa}}, {{GN}}).
  - Suporte a múltiplos aliases oficiais do Gmail (SendAs) e puxada automática da assinatura configurada.
  - Suporte opcional a CC e CCO com validação rigorosa, priorização de CC sobre CCO e deduplicação por mensagem individual.
  - Prévia realista do cliente de e-mail com aviso administrativo de CCO.
  - Modal de confirmação com cálculo dinâmico e aviso explicativo em âmbar sobre a multiplicação de e-mails para endereços em CC/CCO.
  - Bloqueio preventivo e em tempo de execução quando a cota diária do Google Workspace for insuficiente.
  - Autosave contínuo de assunto, corpo, assinatura, CC e CCO em localStorage.
- **Restrições Técnicas Duráveis**:
  - Teto operacional de 50 contatos principais por lote.
  - Sujeito à cota diária de envio da conta Google Workspace (MailApp.getRemainingDailyQuota()).
  - Backend executado estritamente no runtime Google Apps Script (codigo.gs).

## Brand Commitments

- **Nome**: Disparador de E-mails IPNET By Vivo.
- **Identidade Visual**: SaaS profissional moderno (estilo Linear/Resend), paleta equilibrada com tons índigo/slate, tipografia clara (Inter) e monoespaçada (JetBrains Mono) para tags e indicadores técnicos.
- **Logotipos**: Suporte a URLs diretas ou links Google Drive para logos corporativos em modo claro e escuro.

## Evidence on Hand

- Frontend funcional de alta densidade em Index.html com suporte a Tailwind CSS, alternância de tema claro/escuro e editor rico.
- Backend robusto e validado em codigo.gs / codigo.gs.txt.
- Ausências a não fabricar: não inventar autenticações externas ou dependências de bancos de dados que descaracterizem a natividade do Apps Script.

## Product Principles

1. **Controle Total e Previsibilidade**: O usuário sempre visualiza com exatidão como o e-mail chegará ao destinatário (prévia) e quantos disparos serão consumidos na cota antes de qualquer envio.
2. **Entregabilidade e Respeito aos Limites**: Proteger a saúde da conta e a reputação do remetente por meio de sanitização HTML, intervalo de segurança anti-spam e validação estrita de formatos de e-mail.
3. **Leveza com Zero Fricção**: Todo o ciclo — da colagem de planilhas ao disparo — deve ocorrer em poucos cliques, sem passos burocráticos ou configurações externas pesadas.

## Accessibility & Inclusion

- Suporte integrado a temas Claro e Escuro com tokens de alto contraste.
- Validação visual clara e acessível para campos com erro (.invalid-email e mensagens inline descritivas).
- Foco em legibilidade e hierarquia da informação tanto em monitores widescreen quanto em dispositivos móveis.
