# Guia de Relatórios GA4 — Ferreira Odontologia

## Resposta à pergunta: "Consigo importar os relatórios prontos?"

**Resposta honesta e direta:**

| Tipo | Dá para importar de um arquivo? |
|---|---|
| Relatórios / Explorações do GA4 | ❌ **Não.** O GA4 **não tem** importação por arquivo (diferente do Google Tag Manager). Explorações só podem ser compartilhadas por link **dentro da mesma propriedade**. |
| Painel no **Looker Studio** | ✅ **Sim.** O Looker Studio (ferramenta gratuita do Google) permite copiar um painel a partir de um link de modelo e conectar nos seus dados. **É o caminho real para "relatório importável".** |

Por isso este guia tem **duas partes**:
- **Parte A** — passo a passo para montar as Explorações no GA4 (5 min cada, feito uma vez)
- **Parte B** — montar o painel visual no Looker Studio (o "dashboard" de verdade, compartilhável)

---

# PARTE A — Explorações no GA4

Acesse **Explorar → Criar exploração em branco**. Faça uma vez cada.

## A.1 — Funil de Conversão por Canal

**Tipo:** Exploração de funil

**Etapas:**
1. `page_view` — Entrou no site
2. `secao_visitada` — Viu uma seção (engajou)
3. `faq_abriu` OU `form_start` — Demonstrou interesse
4. `generate_lead` — **Converteu**

**Divisão (breakdown):** dimensão personalizada **Origem do canal**

> Responde: de cada 100 pessoas do Instagram, quantas viram serviços, quantas demonstraram interesse, quantas converteram — e como isso compara com Facebook e tráfego direto.

## A.2 — Conversão por Botão de WhatsApp

**Tipo:** Tabela livre (Free form)

- **Linhas:** dimensão **Botão WhatsApp** (`botao`)
- **Colunas:** dimensão **Origem do canal**
- **Métrica:** Contagem de eventos (filtrar por `event_name = clique_whatsapp`)

> Responde: qual botão converte mais, e se isso muda por canal. Decisão de design baseada em dado.

## A.3 — Interesse por Serviço

**Tipo:** Tabela livre

- **Linhas:** dimensão **Serviço** (`servico`) + **Pergunta FAQ** (`pergunta`)
- **Métrica:** Contagem de eventos
- **Filtro:** `event_name` contém `formulario_enviado` ou `faq_abriu`

> Responde: qual tratamento gera mais procura (lentes? botox? clareamento?). Direciona conteúdo e anúncio.

## A.4 — Qualidade de Engajamento

**Tipo:** Tabela livre

- **Linhas:** **Origem do canal**
- **Métricas:** Usuários, `tempo_pagina` (médio), `scroll_profundidade` (90%), taxa de `generate_lead`

> Responde: qual canal traz gente que realmente lê o site vs. quem entra e sai. Tráfego de qualidade ≠ volume de tráfego.

## A.5 — Abandono de Formulário

**Tipo:** Funil

1. `form_start` — Começou a preencher
2. `formulario_enviado` — Concluiu

> A diferença entre as etapas = taxa de abandono. Se for alta, o formulário tem fricção.

---

# PARTE B — Painel no Looker Studio (dashboard compartilhável)

O Looker Studio é gratuito e gera o painel visual bonito que pode ser compartilhado por link, exportado em PDF e atualizado sozinho.

## Passo a passo

1. Acesse **lookerstudio.google.com**
2. Clique em **Criar → Relatório**
3. Em "Adicionar dados", escolha o conector **Google Analytics**
4. Selecione a conta **Ferreira Odontologia → Site Ferreira Odontologia → fluxo G-0HZ0S7NNT0**
5. Monte os blocos abaixo (cada um é um "gráfico" que você adiciona):

### Bloco 1 — Cartões de resumo (Scorecards)
| Métrica | Configuração |
|---|---|
| Total de Leads | Métrica: contagem de eventos · Filtro: `event_name = generate_lead` |
| Cliques WhatsApp | Filtro: `event_name = clique_whatsapp` |
| Formulários enviados | Filtro: `event_name = formulario_enviado` |
| Usuários ativos | Métrica padrão: Usuários ativos |

### Bloco 2 — Gráfico de pizza: Leads por origem
- Dimensão: `origem` (dimensão personalizada)
- Métrica: contagem de eventos
- Filtro: `event_name = generate_lead`

### Bloco 3 — Série temporal: Leads por dia
- Dimensão: Data · Métrica: contagem de `generate_lead`

### Bloco 4 — Tabela: Botão de WhatsApp x Origem
- Linha: `botao` · Coluna/segmento: `origem` · Métrica: contagem `clique_whatsapp`

### Bloco 5 — Mapa de calor: Serviços mais procurados
- Dimensão: `servico` · Métrica: contagem `formulario_enviado`

6. **Compartilhar:** botão "Compartilhar" → link. Pode mandar pro celular, abrir no Power BI por screenshot, ou agendar envio por e-mail automático.

> **Importante:** as dimensões `origem`, `botao`, `servico` etc. só aparecem no Looker Studio **depois** de criadas como Dimensões Personalizadas no GA4 (ver DOCUMENTACAO-TRACKING.md seção 4) **e** depois de ~24-48h coletando dados.

---

## Sobre integração com Power BI / Databricks (futuro)

Quando o volume justificar, o caminho profissional é:

1. **GA4 → BigQuery** (exportação nativa gratuita até 1M eventos/dia) — Admin → Vinculações → BigQuery
2. **BigQuery → Power BI / Databricks** via conector nativo

Isso dá o dado **bruto, evento a evento**, cruzável com qualquer fonte. É a Fase 3. Só faz sentido depois que GA4 + Looker Studio estiverem rodando e gerando decisão.

---

## Checklist de ativação (ordem)

- [ ] Marcar `generate_lead`, `clique_whatsapp`, `formulario_enviado` como conversão (Admin → Eventos)
- [ ] Criar as 10 dimensões personalizadas (DOCUMENTACAO-TRACKING.md seção 4)
- [ ] Ativar Sinais do Google + dados demográficos (Admin → Coleta de dados)
- [ ] Aguardar 24-48h de coleta
- [ ] Montar as 5 Explorações (Parte A)
- [ ] Montar o painel Looker Studio (Parte B)
- [ ] (Futuro) Vincular BigQuery quando escalar
