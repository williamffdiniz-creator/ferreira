# Documentação de Tracking — Ferreira Odontologia e Saúde

**Propriedade GA4:** `G-0HZ0S7NNT0`
**Site:** https://ferreiraodontologiaesaude.com.br
**Versão do motor de tracking:** v3
**Padrão de referência:** GA4 recommended events + taxonomia de e-commerce (modelo Magazine Luiza / Casas Bahia)

---

## 1. Como o tracking está organizado

Todo evento do site passa por **um único motor central** (`window.track()`), instalado no `<head>` do `index.html`. Esse helper anexa automaticamente, em **todos** os eventos, os dados de atribuição:

| Parâmetro automático | O que é |
|---|---|
| `origem` | Canal de entrada (instagram, facebook, home, direto, google_organico, ou UTM) |
| `canal_midia` | social / organic / direct / cpc (derivado da origem ou do `utm_medium`) |
| `campanha` | Nome da campanha (`utm_campaign`), quando vier de anúncio |

> **Por que isso importa:** é o mesmo princípio que grandes e-commerces usam — um *wrapper* único garante que nenhum evento seja disparado sem atribuição. Você sempre consegue responder "isso veio de qual canal?".

### Cascata de atribuição (ordem de prioridade)

1. `?origem=` na URL (as páginas `/instagram`, `/facebook`, `/home`)
2. `utm_source` (links de anúncio pago)
3. `gclid` / `fbclid` (clique de Google Ads / Meta Ads)
4. Sessão anterior (sessionStorage — mantém a origem durante a navegação)
5. Referrer (instagram.com, facebook.com, google.)
6. `direto` (sem nenhuma das anteriores)

---

## 2. Catálogo completo de eventos

### 2.1 Conversões (marcar como conversão no GA4)

| Evento | Quando dispara | Parâmetros | Valor |
|---|---|---|---|
| `generate_lead` | Clique em WhatsApp **ou** envio do formulário | `metodo` (whatsapp/formulario), `servico`, `botao` | `value:1`, `currency:BRL` |
| `clique_whatsapp` | Qualquer um dos 16 botões de WhatsApp | `botao` (local do clique) | — |
| `formulario_enviado` | Formulário de contato enviado com sucesso | `servico` | — |

> `generate_lead` é um **evento recomendado nativo do GA4**. Marcando ele como conversão, os relatórios de aquisição/conversão do GA4 funcionam automaticamente, sem configuração extra.

### 2.2 Micro-conversões e engajamento

| Evento | Quando dispara | Parâmetros |
|---|---|---|
| `form_start` | Usuário clica no 1º campo do formulário | `formulario` |
| `form_erro` | Tentou enviar formulário com campo obrigatório vazio | `formulario`, `campos` |
| `faq_abriu` | Abre uma das 5 perguntas do FAQ | `pergunta` |
| `secao_visitada` | Rola até Sobre / Serviços / Estrutura / Contato | `secao` |
| `scroll_profundidade` | Atinge 25%, 50%, 75%, 90% da página | `porcentagem` |
| `tempo_pagina` | 30s, 60s, 2min, 5min de **tempo engajado** | `segundos` |
| `depoimento_navegar` | Navega no carrossel de depoimentos | `acao` (anterior/proximo/ponto_N) |
| `menu_navegacao` | Clica num item de menu (topo, mobile, rodapé) | `destino` |
| `clique_social` | Clica no Instagram do rodapé | `rede` |
| `clique_mapa` | Clica no endereço / Google Maps | — |
| `saida_pagina` | Usuário sai ou troca de aba | `profundidade_max` |

> **Tempo engajado vs. tempo na página:** o `tempo_pagina` **pausa** quando a aba está minimizada ou em segundo plano. Isso evita inflar a métrica — exatamente como os grandes players medem engajamento real.

### 2.3 E-commerce (catálogo de produtos)

> ⚠️ A seção de produtos está **oculta** atualmente (`display:none` no HTML). O tracking já está instalado e dispara automaticamente quando a seção for reativada (trocar `display:none` por `display:block`).

| Evento | Quando dispara | Padrão GA4 |
|---|---|---|
| `view_item_list` | Catálogo de produtos carrega | Enhanced Ecommerce |
| `select_item` | Clica num produto (vai pro Mercado Livre) | Enhanced Ecommerce |
| `clique_produto` | Idem (versão simplificada p/ relatório rápido) | Custom |
| `filtro_catalogo` | Filtra por categoria | Custom |

Cada `select_item` / `view_item_list` carrega o array `items` no formato oficial GA4 (`item_name`, `item_category`, `item_brand`), pronto para os relatórios de e-commerce nativos.

### 2.4 Saúde técnica

| Evento | Quando dispara | Parâmetros |
|---|---|---|
| `erro_js` | Erro de JavaScript no site | `mensagem`, `arquivo`, `linha` |

---

## 3. Mapa de botões de WhatsApp (parâmetro `botao`)

| Valor de `botao` | Localização no site |
|---|---|
| `header` | Botão "Agendar" do topo (desktop) |
| `menu_mobile` | Botão dentro do menu mobile |
| `hero` | Botão principal da primeira dobra |
| `servico_lentes` | Card "Lentes de Contato Dental" |
| `servico_clareamento` | Card "Clareamento" |
| `servico_implantes` | Card "Implantes" |
| `servico_reabilitacao` | Card "Reabilitação Oral" |
| `servico_faceta` | Card "Faceta em Resina" |
| `servico_botox` | Card "Botox" |
| `servico_preench_facial` | Card "Preenchimento Facial" |
| `servico_preench_labial` | Card "Preenchimento Labial" |
| `servico_skincare` | Card "Skincare / Peeling" |
| `cta_avaliacao` | CTA "Agendar avaliação" |
| `formulario_contato` | Envio do formulário |
| `rodape_telefone` | Telefone no rodapé |
| `rodape_icone` | Ícone WhatsApp no rodapé |
| `botao_flutuante` | Botão verde flutuante (sempre visível) |

> Esse mapa responde a pergunta de ouro: **qual botão converte mais?** Você vai descobrir se o botão flutuante vale mais que os cards de serviço, e ajustar o site com base em dado.

---

## 4. Dimensões personalizadas a criar no GA4

Em **Admin → Dimensões personalizadas → Criar**, escopo *Evento*:

| Nome de exibição | Parâmetro do evento |
|---|---|
| Origem do canal | `origem` |
| Canal de mídia | `canal_midia` |
| Campanha | `campanha` |
| Botão WhatsApp | `botao` |
| Serviço | `servico` |
| Pergunta FAQ | `pergunta` |
| Seção | `secao` |
| Método do lead | `metodo` |
| Produto | `produto` |
| Categoria do produto | `categoria` |

> Sem criar essas dimensões, os dados são coletados mas **não aparecem** nos relatórios. Este é o passo mais esquecido — não pule.

---

## 5. Páginas de origem (`/instagram`, `/facebook`, `/home`)

Cada pasta tem um `index.html` que grava a origem em `sessionStorage` e redireciona para `/?origem=<canal>`. A origem **persiste durante toda a navegação** da pessoa, mesmo que ela vá para outras seções.

| Link para divulgar | Onde usar |
|---|---|
| `ferreiraodontologiaesaude.com.br/instagram` | Bio do Instagram, stories |
| `ferreiraodontologiaesaude.com.br/facebook` | Página do Facebook, posts |
| `ferreiraodontologiaesaude.com.br/home` | QR code, material impresso |

### Para anúncios pagos (futuro)

Use UTMs nos links de anúncio que o tracking captura automaticamente:

```
ferreiraodontologiaesaude.com.br/?utm_source=instagram&utm_medium=cpc&utm_campaign=lentes_marco
```

`gclid` (Google Ads) e `fbclid` (Meta Ads) também são capturados sem nenhuma configuração.

---

## 6. Manutenção

- **Onde mexer:** todo o tracking está em `index.html`, bloco entre `<!-- Motor de Tracking v3 -->` e `<!-- Fim Motor de Tracking -->`, mais os `onclick`/`@click` espalhados.
- **Adicionar um evento novo:** chame `window.track('nome_do_evento', { parametro: valor })`. A atribuição é anexada sozinha.
- **Nunca** use `gtag('event', ...)` direto — sempre `window.track()`, para não perder atribuição.
- Taxonomia: nomes em `snake_case`, minúsculo, português. Mantenha o padrão.
