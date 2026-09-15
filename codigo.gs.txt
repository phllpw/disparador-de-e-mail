/**
 * DISPARADOR DE E-MAILS EM MASSA - IPNET BY VIVO
 * Backend Google Apps Script (codigo.gs)
 * 
 * Funcionalidades:
 * - Validação rigorosa de lotes e cota diária
 * - Sanitização de HTML com preservação de links (href seguros)
 * - Estilização inline compatível com Gmail, Outlook e Apple Mail
 * - Substituição dinâmica de tags: {{Nome}}, {{Empresa}}, {{GN}}
 * - Suporte a múltiplos aliases de envio (SendAs)
 * - Integração automática com assinatura corporativa do Gmail
 */

const CONFIG_DISPARADOR = {
  MAX_CONTATOS: 50,
  MAX_ASSUNTO: 250,
  MAX_CORPO_HTML: 100000,
  MAX_CORPO_TEXTO: 50000,
  MAX_ASSINATURA: 20000,
  DELAY_MS: 300 // Intervalo preventivo entre envios contra bloqueios anti-spam
};

/**
 * CONFIGURAÇÃO DE LOGO DA EMPRESA:
 * Suporta URLs diretas de imagem OU links de compartilhamento do Google Drive.
 * Se deixado em branco (""), a aplicação usará o ícone SVG minimalista da marca.
 */
const CONFIG_LOGOS = {
  LOGO_LIGHT: "https://drive.google.com/file/d/1EszgQNt6_Ivba_uuamUhkc0VdWvfrfgM/view?usp=sharing", // Modo Claro
  LOGO_DARK: "https://drive.google.com/file/d/1qsPDzQ2ZKPdgX1tA7EC_7nGpoDppRABv/view?usp=sharing"   // Modo Escuro
};

/**
 * Ponto de entrada do Web App
 */
function doGet() {
  return HtmlService.createHtmlOutputFromFile('Index')
    .setTitle('Disparador IPNET By Vivo')
    .addMetaTag('viewport', 'width=device-width, initial-scale=1.0')
    .setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL);
}

/**
 * Converte links de compartilhamento do Google Drive em URLs de imagem direta (lh3.googleusercontent.com)
 */
function converterLinkGoogleDrive(url) {
  if (!url || typeof url !== 'string') return '';
  var link = url.trim();
  
  // Formato: drive.google.com/file/d/FILE_ID/view...
  var matchFile = link.match(/\/file\/d\/([a-zA-Z0-9_-]+)/);
  if (matchFile && matchFile[1]) {
    return 'https://lh3.googleusercontent.com/d/' + matchFile[1];
  }
  
  // Formato: drive.google.com/uc?id=FILE_ID
  var matchId = link.match(/[?&]id=([a-zA-Z0-9_-]+)/);
  if (matchId && matchId[1]) {
    return 'https://lh3.googleusercontent.com/d/' + matchId[1];
  }

  return link;
}

/**
 * Teste para forçar o pop-up de permissões e diagnosticar serviços Gmail no Editor do Apps Script.
 */
function testarEAutorizarAssinatura() {
  var email = Session.getActiveUser().getEmail();
  Logger.log("Usuário ativo: " + email);
  
  if (typeof Gmail !== 'undefined' && Gmail.Users && Gmail.Users.Settings && Gmail.Users.Settings.SendAs) {
    var response = Gmail.Users.Settings.SendAs.list("me");
    var sendAsAccounts = response.sendAs || [];
    Logger.log("Encontrados " + sendAsAccounts.length + " alias(es).");
    sendAsAccounts.forEach(function(acc) {
      Logger.log("Alias: " + acc.sendAsEmail + " | Assinatura encontrada: " + (acc.signature ? "Sim" : "Não"));
    });
  } else {
    Logger.log("Serviço Avançado Gmail não detectado. Usando fallback GmailApp.");
  }
}

/**
 * Retorna as informações do usuário ativo, cota diária disponível, aliases configurados,
 * assinatura oficial sincronizada do Gmail e URLs tratadas dos logotipos.
 */
function obterAssinaturaEInfoUsuario() {
  var email = '';
  try {
    email = Session.getActiveUser().getEmail() || Session.getEffectiveUser().getEmail() || '';
  } catch (e) {}

  var cotaRestante = 0;
  try {
    cotaRestante = MailApp.getRemainingDailyQuota();
  } catch (e) {}

  var assinaturaHtml = '';
  var aliases = [];
  var erroGmailApi = null;

  // Busca assinaturas e aliases via Gmail Advanced Service (Gmail API)
  try {
    if (typeof Gmail !== 'undefined' && Gmail.Users && Gmail.Users.Settings && Gmail.Users.Settings.SendAs) {
      var response = Gmail.Users.Settings.SendAs.list("me");
      var sendAsAccounts = response.sendAs || [];
      
      sendAsAccounts.forEach(function(account) {
        aliases.push({
          email: account.sendAsEmail,
          nome: account.displayName || account.sendAsEmail,
          padrao: !!account.isDefault
        });
        
        if (account.isDefault && account.signature) {
          assinaturaHtml = account.signature;
        }
      });

      // Se nenhum tiver marcado como padrão com assinatura, usa a do primeiro disponível
      if (!assinaturaHtml && sendAsAccounts.length > 0) {
        for (var i = 0; i < sendAsAccounts.length; i++) {
          if (sendAsAccounts[i].signature) {
            assinaturaHtml = sendAsAccounts[i].signature;
            break;
          }
        }
      }
    }
  } catch (err) {
    erroGmailApi = String(err && err.message ? err.message : err);
    Logger.log("Aviso ao buscar assinatura via Gmail API: " + erroGmailApi);
  }

  // Fallback de aliases via GmailApp caso a Gmail API não esteja habilitada
  if (aliases.length === 0 && email) {
    aliases.push({ email: email, nome: email, padrao: true });
    try {
      var extraAliases = GmailApp.getAliases();
      if (Array.isArray(extraAliases)) {
        extraAliases.forEach(function(aliasEmail) {
          aliases.push({ email: aliasEmail, nome: aliasEmail, padrao: false });
        });
      }
    } catch (e) {}
  }

  // Tratamento automático dos links do Google Drive para imagem direta
  var logosTratadas = {
    LOGO_LIGHT: converterLinkGoogleDrive(CONFIG_LOGOS.LOGO_LIGHT),
    LOGO_DARK: converterLinkGoogleDrive(CONFIG_LOGOS.LOGO_DARK)
  };

  return {
    email: email,
    cotaRestante: cotaRestante,
    assinaturaHtml: assinaturaHtml,
    aliases: aliases,
    logos: logosTratadas,
    erroGmailApi: erroGmailApi
  };
}

/**
 * Função principal de processamento e envio em lote
 */
function enviarEmailsWeb(contatos, assunto, corpoHtmlTemplate, corpoTextoTemplate, assinatura, aliasRemetente, nomeRemetente, cc, cco) {
  var lock = LockService.getScriptLock();
  var inicioTempo = new Date().getTime();

  // Bloqueio de concorrência por 5 segundos
  if (!lock.tryLock(5000)) {
    throw new Error('Já existe um disparo em andamento para esta conta. Aguarde alguns instantes e tente novamente.');
  }

  try {
    validarEntradaGeral(contatos, assunto, corpoHtmlTemplate, corpoTextoTemplate, assinatura);

    var analiseCc = processarListaEmails(cc);
    var analiseCco = processarListaEmails(cco);

    if (analiseCc.erros.length > 0 || analiseCco.erros.length > 0) {
      var errosFormatados = [];
      if (analiseCc.erros.length > 0) {
        errosFormatados.push('CC: ' + analiseCc.erros.join(', '));
      }
      if (analiseCco.erros.length > 0) {
        errosFormatados.push('CCO: ' + analiseCco.erros.join(', '));
      }
      throw new Error('Endereço(s) inválido(s) detectado(s) em opções de envio: ' + errosFormatados.join(' | '));
    }

    // Se um mesmo endereço estiver em CC e CCO, priorizar CC e remover do CCO
    var ccValidos = analiseCc.validos;
    var ccoValidos = analiseCco.validos.filter(function(email) {
      return ccValidos.indexOf(email) === -1;
    });

    var assuntoLimpo = String(assunto).trim();
    var corpoHtmlSeguro = estilizarHtmlEmail(sanitizarHtmlEditor(corpoHtmlTemplate));
    var corpoTextoSeguro = normalizarTextoPlano(corpoTextoTemplate);
    var assinaturaProcessada = processarAssinatura(assinatura);
    var contatosProcessados = prepararContatos(contatos);

    if (contatosProcessados.validos.length === 0) {
      throw new Error('Nenhum destinatário válido foi encontrado para envio.');
    }

    // Cálculo da quantidade real estimada de destinatários do lote
    var consumoEstimadoQuota = 0;
    for (var k = 0; k < contatosProcessados.validos.length; k++) {
      var emailCliente = contatosProcessados.validos[k].email;
      var ccParaEste = ccValidos.filter(function(e) { return e !== emailCliente; });
      var ccoParaEste = ccoValidos.filter(function(e) { return e !== emailCliente; });
      consumoEstimadoQuota += (1 + ccParaEste.length + ccoParaEste.length);
    }

    var cotaDisponivel = MailApp.getRemainingDailyQuota();
    if (consumoEstimadoQuota > cotaDisponivel) {
      throw new Error(
        'Cota insuficiente. Este disparo utilizará aproximadamente ' + consumoEstimadoQuota +
        ' destinatários, mas sua conta possui apenas ' + cotaDisponivel + ' disponíveis.'
      );
    }

    var enviados = 0;
    var falhas = contatosProcessados.avisos.slice();

    for (var i = 0; i < contatosProcessados.validos.length; i++) {
      // Delay preventivo entre envios contra trigger de anti-spam do Google
      if (i > 0 && CONFIG_DISPARADOR.DELAY_MS > 0) {
        Utilities.sleep(CONFIG_DISPARADOR.DELAY_MS);
      }

      var cliente = contatosProcessados.validos[i];
      var dados = {
        nome: cliente.nome,
        empresa: cliente.empresa,
        gn: cliente.gn || ''
      };

      var assuntoFinal = substituirVariaveisTexto(assuntoLimpo, dados);
      var htmlFinal = substituirVariaveisHtml(corpoHtmlSeguro, dados) + assinaturaProcessada.html;
      var textoFinal = substituirVariaveisTexto(corpoTextoSeguro, dados) + assinaturaProcessada.texto;

      var opcoesEnvio = {
        htmlBody: montarDocumentoEmail(htmlFinal)
      };

      if (aliasRemetente && typeof aliasRemetente === 'string' && aliasRemetente.indexOf('@') > 0) {
        opcoesEnvio.from = aliasRemetente;
      }

      if (nomeRemetente && typeof nomeRemetente === 'string' && nomeRemetente.trim()) {
        opcoesEnvio.name = nomeRemetente.trim();
      }

      // Durante cada envio, caso o e-mail principal do destinatário também esteja na lista de CC/CCO,
      // removê-lo daquela mensagem para não enviar duplicado para a mesma pessoa.
      var ccFinal = ccValidos.filter(function(e) { return e !== cliente.email; });
      var ccoFinal = ccoValidos.filter(function(e) { return e !== cliente.email; });

      if (ccFinal.length) {
        opcoesEnvio.cc = ccFinal.join(',');
      }

      if (ccoFinal.length) {
        opcoesEnvio.bcc = ccoFinal.join(',');
      }

      try {
        GmailApp.sendEmail(cliente.email, assuntoFinal, textoFinal, opcoesEnvio);
        enviados++;
      } catch (erro) {
        falhas.push(cliente.email + ': ' + mensagemSeguraDoErro(erro));
      }
    }

    var fimTempo = new Date().getTime();

    return {
      sucesso: enviados > 0,
      quantidade: enviados,
      totalValidos: contatosProcessados.validos.length,
      duplicadosIgnorados: contatosProcessados.duplicados,
      erros: falhas,
      cotaRestante: MailApp.getRemainingDailyQuota(),
      aliasUtilizado: aliasRemetente || Session.getActiveUser().getEmail(),
      tempoExecucaoMs: (fimTempo - inicioTempo),
      ccUtilizados: ccValidos,
      ccoUtilizados: ccoValidos,
      consumoEstimadoQuota: consumoEstimadoQuota
    };
  } finally {
    lock.releaseLock();
  }
}

/**
 * Validação dos parâmetros recebidos da interface
 */
function validarEntradaGeral(contatos, assunto, corpoHtml, corpoTexto, assinatura) {
  if (!Array.isArray(contatos) || contatos.length === 0) {
    throw new Error('Nenhum contato foi adicionado à tabela de envio.');
  }

  if (contatos.length > CONFIG_DISPARADOR.MAX_CONTATOS) {
    throw new Error('Limite máximo de ' + CONFIG_DISPARADOR.MAX_CONTATOS + ' contatos excedido por lote.');
  }

  var assuntoNormalizado = String(assunto || '').trim();
  if (!assuntoNormalizado) {
    throw new Error('O assunto do e-mail não pode ficar vazio.');
  }
  if (assuntoNormalizado.length > CONFIG_DISPARADOR.MAX_ASSUNTO) {
    throw new Error('O assunto excedeu o limite de ' + CONFIG_DISPARADOR.MAX_ASSUNTO + ' caracteres.');
  }

  var htmlNormalizado = String(corpoHtml || '').trim();
  var textoNormalizado = String(corpoTexto || '').trim();
  if (!htmlNormalizado || !textoNormalizado) {
    throw new Error('O corpo da mensagem não pode ficar vazio.');
  }
  if (htmlNormalizado.length > CONFIG_DISPARADOR.MAX_CORPO_HTML) {
    throw new Error('O conteúdo visual do e-mail excedeu o tamanho máximo suportado.');
  }
  if (textoNormalizado.length > CONFIG_DISPARADOR.MAX_CORPO_TEXTO) {
    throw new Error('O texto do e-mail excedeu o limite permitido.');
  }

  if (String(assinatura || '').length > CONFIG_DISPARADOR.MAX_ASSINATURA) {
    throw new Error('A assinatura ultrapassou o limite máximo permitido.');
  }
}

/**
 * Higieniza a lista de contatos, deduplicando por e-mail e aplicando fallbacks
 */
function prepararContatos(contatos) {
  var vistos = {};
  var validos = [];
  var avisos = [];
  var duplicados = 0;

  contatos.forEach(function(contato, indice) {
    var nome = String((contato && contato.nome) || '').trim() || 'Prezado(a)';
    var email = String((contato && contato.email) || '').trim().toLowerCase();
    var empresa = String((contato && contato.empresa) || '').trim() || 'sua empresa';
    var gn = String((contato && contato.gn) || '').trim();

    if (!emailValido(email)) {
      avisos.push('Linha ' + (indice + 1) + ' ignorada: formato de e-mail inválido.');
      return;
    }

    if (vistos[email]) {
      duplicados++;
      avisos.push('Linha ' + (indice + 1) + ' ignorada: e-mail duplicado (' + email + ').');
      return;
    }

    vistos[email] = true;
    validos.push({
      nome: limitarTexto(nome, 150),
      email: email,
      empresa: limitarTexto(empresa, 200),
      gn: limitarTexto(gn, 150)
    });
  });

  return {
    validos: validos,
    avisos: avisos,
    duplicados: duplicados
  };
}

function emailValido(email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(String(email || ''));
}

/**
 * Processa e valida listas de e-mails (como CC e CCO)
 * - Suporta múltiplos separadores (, ou ;)
 * - Aplica trim
 * - Converte para minúsculas para deduplicação
 * - Remove entradas vazias
 * - Valida com emailValido()
 * - Remove duplicados
 * - Retorna { validos: [...], erros: [...] }
 */
function processarListaEmails(entrada) {
  if (!entrada) return { validos: [], erros: [] };

  var raw = '';
  if (typeof entrada === 'string') {
    raw = entrada;
  } else if (Array.isArray(entrada)) {
    raw = entrada.join(',');
  } else {
    raw = String(entrada);
  }

  var pedacos = raw.split(/[,;]/);
  var validos = [];
  var erros = [];
  var vistos = {};

  for (var i = 0; i < pedacos.length; i++) {
    var item = pedacos[i].trim().toLowerCase();
    if (!item) continue;

    if (!emailValido(item)) {
      erros.push(item);
    } else if (!vistos[item]) {
      vistos[item] = true;
      validos.push(item);
    }
  }

  return {
    validos: validos,
    erros: erros
  };
}

function limitarTexto(texto, limite) {
  texto = String(texto || '');
  return texto.length > limite ? texto.substring(0, limite) : texto;
}

/**
 * Sanitiza o HTML do editor garantindo segurança e PRESERVANDO links <a href="..."> válidos.
 */
function sanitizarHtmlEditor(html) {
  var resultado = String(html || '');

  // Remove comentários HTML
  resultado = resultado.replace(/<!--[\s\S]*?-->/g, '');

  // Remove tags ativas e perigosas
  resultado = resultado.replace(
    /<(script|style|iframe|object|embed|form|input|button|textarea|select|option|meta|link)[\s\S]*?<\/\1\s*>/gi,
    ''
  );
  resultado = resultado.replace(
    /<(script|style|iframe|object|embed|form|input|button|textarea|select|option|meta|link)[^>]*\/?\s*>/gi,
    ''
  );

  // Remove manipuladores de evento (onclick, onerror, onload, etc.) e links javascript:
  resultado = resultado.replace(/\son\w+\s*=\s*(['"]).*?\1/gi, '');
  resultado = resultado.replace(/\son\w+\s*=\s*[^>\s]+/gi, '');
  resultado = resultado.replace(/href\s*=\s*(['"])\s*javascript:.*?\1/gi, 'href="#"');

  var permitidas = {
    p: true,
    div: true,
    br: true,
    strong: true,
    b: true,
    em: true,
    i: true,
    u: true,
    s: true,
    strike: true,
    ul: true,
    ol: true,
    li: true,
    span: true,
    a: true,
    h1: true,
    h2: true,
    h3: true,
    blockquote: true,
    hr: true
  };

  // Filtro de tags mantendo atributos seguros para links <a>
  resultado = resultado.replace(/<\/?([a-z0-9]+)([^>]*)>/gi, function(tagCompleta, nome, atributos) {
    nome = nome.toLowerCase();
    if (!permitidas[nome]) return '';

    var fechamento = /^<\//.test(tagCompleta);
    if (fechamento) return '</' + nome + '>';
    if (nome === 'br') return '<br>';
    if (nome === 'hr') return '<hr style="border:0;border-top:1px solid #e2e8f0;margin:16px 0;">';

    // Para links, preserva e sanitiza o href
    if (nome === 'a') {
      var matchHref = atributos.match(/href=(['"])(.*?)\1/i);
      var url = matchHref ? matchHref[2].trim() : '';
      if (url && !/^javascript:/i.test(url)) {
        return '<a href="' + escaparHtml(url) + '" target="_blank" rel="noopener noreferrer" style="color:#4f46e5;text-decoration:underline;">';
      }
      return '<a>';
    }

    return '<' + nome + '>';
  });

  return resultado.trim();
}

/**
 * Adiciona estilos inline elegantes e compatíveis com clientes de e-mail modernos
 */
function estilizarHtmlEmail(html) {
  return String(html || '')
    .replace(/<p>/g, "<p style='margin:0 0 14px;line-height:1.65;color:#1e293b;font-size:15px;'>")
    .replace(/<div>/g, "<div style='margin:0 0 10px;line-height:1.65;color:#1e293b;font-size:15px;'>")
    .replace(/<ul>/g, "<ul style='margin:0 0 16px;padding-left:24px;list-style-type:disc;color:#1e293b;'>")
    .replace(/<ol>/g, "<ol style='margin:0 0 16px;padding-left:24px;list-style-type:decimal;color:#1e293b;'>")
    .replace(/<li>/g, "<li style='display:list-item;margin:0 0 8px;line-height:1.55;color:#1e293b;font-size:15px;'>")
    .replace(/<blockquote>/g, "<blockquote style='margin:16px 0;padding:10px 18px;border-left:4px solid #6366f1;background:#f8fafc;color:#475569;font-style:italic;'>")
    .replace(/<h1>/g, "<h1 style='font-size:20px;font-weight:700;color:#0f172a;margin:0 0 16px;line-height:1.3;'>")
    .replace(/<h2>/g, "<h2 style='font-size:18px;font-weight:700;color:#0f172a;margin:0 0 14px;line-height:1.3;'>")
    .replace(/<h3>/g, "<h3 style='font-size:16px;font-weight:600;color:#0f172a;margin:0 0 12px;line-height:1.3;'>");
}

/**
 * Envelope HTML responsivo do e-mail
 */
function montarDocumentoEmail(conteudo) {
  return "<div style='font-family:-apple-system,BlinkMacSystemFont,\"Segoe UI\",Roboto,Helvetica,Arial,sans-serif;font-size:15px;line-height:1.65;color:#1e293b;max-width:640px;margin:0 auto;padding:16px;'>" +
    conteudo +
    "</div>";
}

/**
 * Processamento e renderização da assinatura
 */
function processarAssinatura(assinatura) {
  var valor = String(assinatura || '').trim();
  if (!valor) {
    return { html: '', texto: '' };
  }

  // Assinatura como URL de imagem direta
  if (/^https?:\/\/[^\s]+$/i.test(valor) && (/\.(png|jpg|jpeg|gif|webp)(\?.*)?$/i.test(valor) || valor.indexOf('googleusercontent') > 0)) {
    return {
      html: "<div style='margin-top:28px;padding-top:16px;border-top:1px solid #e2e8f0;'><img src='" + escaparHtml(valor) + "' alt='Assinatura' style='max-width:480px;width:100%;height:auto;display:block;border:0;'></div>",
      texto: '\n\n'
    };
  }

  // Assinatura já em HTML (ex: vinda do Gmail API)
  if (/<[a-z][\s\S]*>/i.test(valor)) {
    return {
      html: "<div style='margin-top:28px;padding-top:16px;border-top:1px solid #e2e8f0;font-size:13px;line-height:1.5;color:#475569;'>" + valor + "</div>",
      texto: '\n\n' + valor.replace(/<[^>]+>/g, '')
    };
  }

  // Assinatura em texto plano com suporte a negrito **texto**
  var assinaturaEscapada = escaparHtml(valor)
    .replace(/\*\*(.+?)\*\*/g, '<strong>$1</strong>')
    .replace(/\r?\n/g, '<br>');

  return {
    html: "<div style='margin-top:28px;padding-top:16px;border-top:1px solid #e2e8f0;font-size:13px;line-height:1.5;color:#475569;'>" + assinaturaEscapada + "</div>",
    texto: '\n\n' + valor
  };
}

/**
 * Substituição de tags inteligentes
 */
function substituirVariaveisHtml(template, dados) {
  return String(template || '')
    .replace(/{{\s*Nome\s*}}/gi, escaparHtml(dados.nome))
    .replace(/{{\s*Empresa\s*}}/gi, escaparHtml(dados.empresa))
    .replace(/{{\s*GN\s*}}/gi, escaparHtml(dados.gn || ''));
}

function substituirVariaveisTexto(template, dados) {
  return String(template || '')
    .replace(/{{\s*Nome\s*}}/gi, dados.nome)
    .replace(/{{\s*Empresa\s*}}/gi, dados.empresa)
    .replace(/{{\s*GN\s*}}/gi, dados.gn || '');
}

function normalizarTextoPlano(texto) {
  return String(texto || '')
    .replace(/\r\n/g, '\n')
    .replace(/\r/g, '\n')
    .replace(/\n{4,}/g, '\n\n\n')
    .trim();
}

function escaparHtml(valor) {
  return String(valor || '')
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#039;');
}

function mensagemSeguraDoErro(erro) {
  if (erro && erro.message) return String(erro.message);
  return 'Falha não identificada durante a transmissão do e-mail.';
}
