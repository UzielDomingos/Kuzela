# KUZELA — Pesquisa de mercado, concorrência e plano de MVP (Standard Bank Angola)

**Data da pesquisa:** 22 set 2026 · **Autor:** Claude (a pedido do Uziel)
**Legenda:** **[F]** facto com fonte · **[I]** inferência · **[NV]** não verificado · **[D]** decisão proposta

---

## 0. Contexto e limitações (ler primeiro)

**Porque existe este documento.** O Uziel quer passar de uma publicação editorial (Kuzela Research, Fase 1, Canva) para provar que uma *company page* sobre uma empresa angolana, feita com dados reais e rastreáveis, funciona. O laboratório é o Standard Bank Angola (SBA), que começa a negociar na BODIVA a 30/09/2026.

**Contradição com decisões anteriores (aviso numa frase, como pede o handoff):** este pedido **revê a D3** (EQ-01 / “empresa numa página” fora da pauta) **e a D6** (sem código na Fase 1). Proponho revê-las só num âmbito limitado: **uma** company page estática, gerada a partir da Folha de Dados, que **também** alimenta a peça CGD-01 de 30/09. Ficamos com uma pesquisa e dois outputs, sem abrir uma terceira linha de produção.

**Limitações da pesquisa. É preciso saber isto antes de confiar no resto:**
1. **O proxy deste ambiente bloqueou o acesso direto** a ebitdasoftware.com, fiscal.ai, valuesense.io, stocksimplifier.com, koyfin.com, simplywall.st, bodiva.ao, cmc.ao e standardbank.co.ao (HTTP 403 no CONNECT). Só a pesquisa web funcionou. Os factos abaixo vêm de resultados de pesquisa e de excertos indexados, **não de páginas primárias abertas por mim**.
2. **As screenshots do EBITDA Software não chegaram a esta conversa.** Só recebi 5 ficheiros .md. Por isso **não consigo analisar a UI do EBITDA Software** (secções A–T).
3. **Stack técnico do EBITDA Software: não foi possível determinar.** Não consegui ler o HTML nem os headers. Fica um método de 10 minutos para o determinares tu (§3.4).

---

## 1. Executive Summary

1. **A premissa “ninguém faz dados de empresas angolanas numa plataforma” é falsa.** Já existem pelo menos dois players: **EBITDA Software** (notícias, dados, indicadores, cotações e volumes em tempo real de todas as cotadas BODIVA, simulador de investimento com comissões e impostos) [F] e **Kitadinvest** (“Hub Financeiro”: cotações, demonstrações financeiras e métricas de BAI, BFA, BCGA, UNITEL, ENSA e BODIVA) [F]. A BODIVA tem dashboard, simulador e carteira do investidor [F].
2. **O mercado cresceu de repente, mas continua minúsculo.** Cotadas em ações: BAI, BFA, BCGA, ENSA, BODIVA, **Unitel** (admitida a 29/07/2026, 11.264 novos acionistas) e **Standard Bank** (a partir de 30/09) = **7 empresas** [F]. É um universo pequeno o suficiente para ser coberto **inteiro e bem**.
3. **Seis das sete cotadas são do setor financeiro** (5 bancos/financeiras + 1 seguradora); a exceção é a Unitel. Um modelo de dados *bank-first* cobre ~70% do universo à primeira.
4. **O espaço livre não é “ter dados”, é “dados verificáveis e explicados”.** Não encontrei evidência de que o EBITDA Software ou o Kitadinvest liguem cada número à página do relatório, nem de que expliquem o motor económico de um banco [NV, a confirmar com auditoria de 30 min, §3.4].
5. **Posicionamento proposto:** *a camada verificável de explicação das empresas cotadas angolanas*: cada número clicável até à página do PDF, com a leitura em português e a lógica económica do setor. Não é um terminal, não tem cotações ao segundo, não tem scores nem BUY/SELL.
6. **Maior vantagem:** a Kuzela Research já produz o conteúdo editorial (CGD-01) que precisa exatamente destes dados. A distribuição (Instagram/LinkedIn) leva as pessoas à company page. Os concorrentes locais têm dados mas, aparentemente, não têm voz editorial [I].
7. **Maior risco:** a monetização. 7 cotadas e liquidez fina não sustentam uma subscrição B2C. A Stears (Nigéria) fechou a divisão consumer e virou enterprise [F]. Nesta fase, a company page constrói **autoridade**, não receita.
8. **Maior risco técnico:** consolidado vs individual, reexpressões entre anos, escalas (milhares vs milhões de Kz), histórico anterior a 2020 com eventuais efeitos de hiperinflação (IAS 29) [NV] e PDFs sem estrutura. Não há API de fundamentais [I, forte].
9. **O que torna a Kuzela defensável:** um dicionário de conceitos angolano (CONTIF/IFRS → conceito Kuzela) e uma base de factos com a página de cada número. Isto acumula e é chato de replicar. A UI replica-se numa semana.
10. **Company ≠ Security.** Na v0.1 não é precisa uma entidade Security: uma empresa, uma ação, preço de IPO. Passa a ser necessária quando houver obrigações do mesmo emitente, várias classes de ações ou séries de preços diárias.
11. **MVP:** *Standard Bank Angola, Company Page v0.1*. Uma página estática com **5 anos de dados reais (2021–2025)**, ~10 métricas, a cascata do motor bancário e **provenance número a número**. Stack: Python + CSV + HTML estático, sem base de dados e sem backend.
12. **A menor coisa que prova a tese:** uma pessoa que nunca leu um Relatório e Contas responde às 8 perguntas do teste em **menos de 5 minutos** e consegue abrir a página do PDF de onde veio pelo menos um número. Se isto falhar com dados reais, nenhum design salva o produto.
13. **Método visual:** *data-first, ugly-first*. Nada de Figma para o produto. Cada componente nasce em HTML com dados reais, é testado com uma pessoa e só depois é congelado. Canva continua a ser só para as peças sociais.
14. **O sprint de 14 dias alinha-se com a peça de 30/09.** Os dias 1–5 (extração e métricas) servem a CGD-01 e a company page ao mesmo tempo.

---

## 2. Competitive Landscape

### 2.1 Tabela resumo

| Concorrente | Utilizador-alvo | Produto core | Objeto principal | Profundidade / histórico | Provenance | Market data | Analytics | Filosofia UX | Modelo | Kuzela aprende | Kuzela evita |
|---|---|---|---|---|---|---|---|---|---|---|---|
| **EBITDA Software** (AO) | Investidor de retalho BODIVA | Notícias + cotações + indicadores + simulador [F] | Mercado / ação [I] | NV | NV | Cotações, variações, volumes “em tempo real” [F] | Indicadores [F]; resto NV | NV (sem screenshots) | NV | Existe procura local suficiente para alguém construir isto | Competir em cotações ao segundo num mercado ilíquido |
| **Kitadinvest** (AO) | Investidor de retalho BODIVA | Cotações + demonstrações + métricas por ação [F] | Ação (`/stocks/BFA`) [F] | Demonstrações presentes [F]; anos NV | NV | Cotações [F] | Métricas [F] | NV | NV | Mesmo universo, mesmo público: é o benchmark direto | Ser “mais um” hub de tabelas |
| **Fiscal.ai** (ex-FinChat) | Investidor sério / pro | Dados institucionais + segmentos/KPIs + copiloto IA [F] | Empresa | Profunda; KPIs de segmentos a partir de filings [F] | Click-through para filings (EUA, plano Enterprise); citações nas respostas IA [F] | Sim | Screens, valuation, estimativas [F] | Infraestrutura primeiro | Pro $49/mês, Max $99/mês; API/MCP à parte [F] | Provenance e KPIs por segmento são o que dá rigor | IA conversacional antes de dados limpos |
| **ValueSense** | Retalho value investor | Valor intrínseco + screener + scores [F] | Ação | Média | NV | Sim | DCF, relativo, scores ML, “rating gauges” coloridos [F] | Dashboard de investidor | Freemium [I] | Componente Number (label → valor → contexto) (já na D1) | Gauges, scores, “undervalued” |
| **Stock Simplifier** | Retalho, iniciante-intermédio | Fluxo guiado Business → Moat → Growth → Management → Risk → Valuation [F] | Empresa (análise guiada) | 10+ anos, dados Fiscal.ai [F] | “Every data point sourced” (via Fiscal.ai) [F] | Sim | 5 métodos de valuation; decisão buy/watch/pass [F] | Research simplificado, ordem narrativa | Assinatura [I] | Ordem narrativa de perguntas (entender → analisar) | Terminar em buy/watch/pass |
| **Koyfin** | Pro / retalho avançado | Terminal de mercado sobre Capital IQ [F] | Ticker / dashboard | 10+ anos [F] | Via filings/transcripts [F] | Muito forte | Gráficos, screeners, macro [F] | Bloomberg-lite | Freemium | Small multiples, séries longas | Densidade de terminal |
| **TIKR** | Retalho fundamental | Financials globais 20 anos [F] | Empresa | 20 anos no Pro ($659/ano, segundo review) [F] | NV | Sim | Estimativas, valuation | Tabelas densas | Freemium | Histórico longo mostra ciclos | Tabela como única interface |
| **Simply Wall St** | Iniciante | Relatórios infográficos, “Snowflake” de 5 dimensões [F] | Empresa | Média | Baixa [I] | Sim | Fair value automático, scorecards [F] | Simplificação visual | Grátis 5 relatórios/mês; ~$10–20/mês [F] | Progressive disclosure funciona para leigos | Score sintético = simplicidade artificial |
| **Finviz** | Trader / retalho EUA | Screener + heatmaps [F] | Mercado / lista | Baixa | Baixa | Forte; heatmaps grátis [F] | 60+ filtros grátis [F] | Densidade | Grátis + Elite | Heatmap = “o que se mexeu hoje” num olhar | Heatmap com 7 ações é decoração |
| **TradingView** | Trader | Gráficos + fundamentais (FactSet) [F] | Gráfico de preço | Média | FactSet, metodologia própria [F] | Muito forte | Fundamental Graphs [F] | Gráfico primeiro | Freemium | Comparar métricas no tempo | Preço como objeto central |
| **AlphaSense** | Enterprise (fundos, corporate) | Pesquisa em 500M+ documentos [F] | Documento / tema | Documental | Citação ao documento é o produto [F] | Não é o foco | Pesquisa semântica | Documento primeiro | $10–40k/seat/ano (reportado) [F] | O documento original é o ativo | Nada a replicar agora |
| **FT / Economist** | Leitor informado | Jornalismo + dataviz | Artigo / gráfico | — | Linha de fonte em todos os gráficos | — | — | Título-conclusão, cor única, anotação | Subscrição | Hierarquia, *Visual Vocabulary* do FT [F] | — |
| **BODIVA / CMC** | Investidores, emitentes | Dados oficiais, dashboard, simulador, carteira [F]; CMC: regulação da divulgação [F] | Mercado / documento | Boletins e relatórios em PDF | São a fonte | Oficial, sem API pública conhecida [NV] | Não | Institucional | — | São a fonte primária | Replicar o que já dão de graça |
| **Stears** (NG) | Hoje: fundos e corporates | Market intelligence africana [F] | Mercado / país | — | — | — | — | — | Enterprise; fechou o consumer [F] | Media → dados funciona, mas monetiza em B2B | Construir plataforma de dados antes de ter autoridade |

### 2.2 Respostas às 19 perguntas, sintetizadas

- **O que vendem, na prática:** Fiscal/Koyfin/TIKR vendem *acesso a dados limpos*. ValueSense/Simply Wall St/Stock Simplifier vendem *uma conclusão rápida* (score, fair value, buy/watch/pass). AlphaSense vende *encontrar o documento*. EBITDA/Kitadinvest vendem *acompanhar a bolsa angolana*.
- **Objeto principal:** nos globais é a **empresa**, com a ação como atributo. Nos locais parece ser a **ação/mercado** [I, pelo URL `/stocks/BFA` e pela descrição do EBITDA].
- **Padrão de company page que se repete:** cabeçalho (nome, ticker, preço) → resumo/KPIs → financials (IS/BS/CF, anual/trimestral) → rácios → valuation → ownership/dividendos → documentos. O resumo quase sempre abre com 4–8 números grandes.
- **Provenance:** só o Fiscal.ai (click-through para filings) e o AlphaSense a tratam como produto. Nos outros, a fonte é “o fornecedor de dados” (S&P, FactSet, Capital IQ), não a página do relatório.
- **Infraestrutura real vs UX sobre dados:** Fiscal.ai (feed próprio de segmentos) e AlphaSense são infraestrutura. ValueSense, Simply Wall St e Stock Simplifier (que usa dados Fiscal.ai [F]) são **UX sobre dados comprados**. Em Angola **não há dados para comprar**, e por isso qualquer player local sério tem de fazer a infraestrutura à mão.
- **Complexidade escondida:** reexpressões, mapeamento de rubricas, moeda/escala, consolidado vs individual e períodos fiscais. Ninguém mostra isto, mas é aqui que se erra.
- **Simplicidade artificial:** scores, snowflakes, gauges, “fair value” e “undervalued”. Comprimem juízos discutíveis num sinal que parece objetivo. Num mercado com 7 ações e liquidez fina seria **rigor fingido**, a mesma conclusão da auditoria de 19 set.
- **Grátis vs pago:** o padrão é resumo e alguns relatórios grátis, com histórico longo, exportação e screener avançado pagos.

---

## 3. EBITDA Software — Deep Dive

### 3.1 O que está verificado [F, via pesquisa web]
- Descreve-se como plataforma de **informação financeira**: notícias, dados e indicadores sobre empresas cotadas, para apoiar a decisão do investidor.
- Cotações, variações e volumes **“em tempo real”** de todas as empresas cotadas na BODIVA.
- Gráficos de desempenho com períodos 1S, 1M, 6M, 1A e Máx.
- **Simulador de investimento** (valor, comissões de corretagem, impostos).
- Menção a funcionalidades de conta/custódia [NV: pode ser conteúdo, não funcionalidade].

### 3.2 O que NÃO consegui verificar
Secções A–T (homepage, navegação, heatmap, pesquisa, company page, summary, financials, valuation, rentabilidade, posição financeira, tooltips, apresentação de fontes, hierarquia, design visual, responsividade, arquitetura): **não verificado**. Não há screenshots nesta conversa e o site está bloqueado pelo proxy.
**Stack (framework, frontend, backend, design system, IA, templates): não foi possível determinar.**

### 3.3 Leitura estratégica, sem inventar a UI [I]
- **Bem feito, pelo que se sabe:** percebeu que o público novo da BODIVA (Unitel: 11.264 novos acionistas; Standard Bank: 10% ao público) precisa de um sítio para acompanhar as ações. O simulador com comissões e impostos resolve uma dor concreta local.
- **Provavelmente superficial:** “tempo real” num mercado com poucos negócios diários por ação dá pouca informação. Um heatmap de 7 ações é decoração.
- **Difícil de replicar:** o feed de cotações da BODIVA, se for oficial e contínuo (como o obtêm: NV).
- **Fácil de replicar:** gráficos de preço, heatmap, simulador (a BODIVA já tem um [F]).
- **Essencial:** preço atual, variação, histórico de preço e dividendos.
- **Dashboard decoration:** heatmap, tickers a correr, “tempo real” sem liquidez.
- **Adaptar para Angola:** o simulador com impostos e comissões angolanos (mais tarde, como explainer EX-01, não como feature).
- **Evitar:** transformar a Kuzela num segundo acompanhador de cotações.

### 3.4 Tarefa de 30 min antes de construir (Dia 0, obrigatória)
Abre o EBITDA Software e o Kitadinvest no teu browser e responde, com screenshot:
1. Um número financeiro (ex.: lucro 2025 do BAI) tem **fonte com documento e página**? Sim/não.
2. Há **explicação** do que o número significa ou da lógica de um banco? Sim/não.
3. Quantos **anos** de demonstrações?
4. Distinguem **consolidado vs individual**?
5. Stack: `Ctrl+U` e procura `/_next/` (Next.js), `__nuxt` (Nuxt), `wp-content` (WordPress), `data-reactroot`/`vite`; extensão **Wappalyzer**; separador Network → headers `server`/`x-powered-by`.

**Regra de decisão:** se algum dos dois já tiver provenance à página **e** explicação, o posicionamento do §4 muda para “o melhor explainer editorial” e a company page desce de prioridade. Se nenhum tiver, avança-se tal como está.

### 3.5 Copiar o princípio, não o artefacto

| Não copiar | Copiar o princípio |
|---|---|
| Cotações “em tempo real” | O utilizador quer saber *o que mudou desde a última vez que olhou* |
| Heatmap | Mostrar o mercado inteiro num olhar. Com 7 cotadas basta uma tabela ordenada |
| Cards de valuation | Informação complexa em blocos comparáveis, com a mesma unidade, período e base |
| BUY/SELL, scores | Chegar depressa a uma conclusão **factual** (título-conclusão + leitura) |
| Snowflake | Progressive disclosure: resumo primeiro, detalhe a pedido |
| “Fonte: S&P Global” | Fonte = documento + página + rubrica original |
| Copiloto IA | Responder a perguntas sobre a empresa com evidência (mais tarde, só sobre dados já verificados) |

---

## 4. Market Gap — onde fica a Kuzela

> **Kuzela should occupy:** *a camada verificável de explicação das empresas cotadas angolanas*. Para cada uma das 7 cotadas, uma página que responde em 5 minutos “como ganha dinheiro, quanto ganha e se está a melhorar”, com cada número rastreável até à página do Relatório e Contas e a mesma investigação publicada como conteúdo editorial.

| Eixo | Fiscal.ai | ValueSense / SWS | Stock Simplifier | EBITDA / Kitadinvest | **Kuzela** |
|---|---|---|---|---|---|
| Promessa | Dados certos | Conclusão rápida | Análise guiada | Acompanhar a bolsa | **Perceber a empresa e poder verificar** |
| Objeto | Empresa | Ação | Empresa | Ação / mercado | **Empresa** |
| Prova | Filing | Score | Framework | Cotação | **Página do PDF** |
| Distribuição | Produto | SEO / produto | Criador (Feroldi) | Produto | **Editorial (Kuzela Research)** |

**Porque é um espaço real e não branding:**
- **Mercado:** 7 empresas. Cobrir tudo com profundidade é exequível para 2 pessoas; um terminal não é.
- **Utilizador:** dezenas de milhares de investidores novos (47.778 contas ativas em 2025 [F, auditoria de 19 set]; +11.264 da Unitel) que estão a *compreender*, não a *negociar*.
- **Dados:** não há API. Quem fizer o trabalho manual com provenance tem um ativo que acumula.
- **Distribuição:** a Kuzela Research já publica sobre estas empresas; a company page é o destino do link.
- **Contexto Angola:** explicar bancos (6 de 7 cotadas são financeiras) exige a lógica margem financeira → imparidades → lucro, que as plataformas genéricas não explicam.

**O que NÃO é:** plataforma de trading, terminal, screener, ferramenta de valuation, nem research pago (Fase 1).

---

## 5. Product Architecture

```
FONTE (PDF R&C, site SBA, BODIVA, CMC)
  ↓  documents.csv          — registo do documento (URL, data, tipo, base)
DADOS
  ↓  facts.csv              — valor as-reported + página + rubrica original + conceito
  ↓  validate.py            — somas, escala, sinais, coerência entre anos
ANALYTICS
  ↓  metrics.py → metrics.json — fórmula + inputs + resultado
INTELLIGENCE
  ↓  readings.yaml          — 1 frase de leitura por métrica/gráfico, escrita por humano
COMPANY
  ↓  build.py → site/standard-bank-angola/index.html (estático)
EDITORIAL
  ↓  o mesmo metrics.json → kuzela_research_charts.py (já existe, D7) → SVG → Canva (CGD-01)
```

**Decisões [D]:**
- **Sem base de dados na v0.1.** CSV versionado em git = a Folha de Dados, com histórico e diffs gratuitos. A base de dados só entra quando houver mais de 3 empresas ou necessidade de consulta.
- **Sem backend.** HTML estático (GitHub Pages ou Artifact).
- **As leituras são escritas por humanos.** A IA ajuda a extrair e a criticar, mas não publica frases de interpretação sem revisão, em linha com o Guia.
- **A extração é assistida, não automatizada.** Claude lê o PDF e propõe linhas, o humano verifica cada uma contra a página. Automatizar só quando a 3.ª empresa mostrar o que se repete.

---

## 6. Data Model v0.1

| Entidade | v0.1 | Campos mínimos |
|---|---|---|
| **Company** | MUST | `company_id` (`sba`), nome, nome curto, setor (`banco`), ISIN/ticker [NV], data de admissão (30/09/2026), website IR |
| **Document** (absorve *Source*) | MUST | `doc_id`, company_id, tipo (`R&C anual`, `R&C semestral`, `prospeto`), período coberto, base (`consolidado`/`individual`), idioma, URL oficial, data de publicação, sha256 do PDF, caminho local |
| **FinancialFact** (absorve *Provenance*) | MUST | `fact_id`, company_id, doc_id, **page**, **label_original** (rubrica tal como está), **value_raw**, unidade (`AOA`), **escala** (`milhares`/`milhões`), `value_aoa` (normalizado), período (`FY2025`), tipo de período (`anual`/`semestral`), base, `concept` (código Kuzela), statement (`DR`/`BAL`/`outro`), `is_restated`, `verified_by`, `verified_at`, notas |
| **Concept** (dicionário) | MUST | `concept` (ex.: `NET_INTEREST_INCOME`), nome PT, definição de 1 linha, sinal esperado. **~25 conceitos, só de banca** |
| **Metric** | MUST (definição em código) | `metric_id`, nome, fórmula legível, conceitos de input, unidade, direção. Calculada no build, não armazenada |
| **MarketData** | SHOULD (mínimo) | Só factos de IPO com fonte: preço da oferta, nº de ações (14M [F]), % em oferta. Série de preços diária: LATER |
| **Source** | fundida em Document | — |
| **Provenance** | fundida em FinancialFact | page + label_original + doc_id + verified_* **são** a provenance |
| **Event** | LATER | IPO, dividendos, mudanças de acionistas. Na v0.1 ficam como texto na página |
| **Security** | LATER | Ver abaixo |
| Segment / KPI operacional (balcões, clientes) | SHOULD | Como FinancialFact com statement=`outro` |
| Peer / Sector aggregate | LATER | Quando existir a 2.ª empresa (BAI) |

**Regras de dados [D]:**
1. **O valor mais recente vence.** Se o R&C 2025 reexprime 2024, usa-se o 2024 reexpresso, com `is_restated=true` e o valor original guardado.
2. **Uma base por página.** Consolidado **ou** individual, nunca misturados. Recomendação: consolidado, se o SBA publicar os dois [NV: o semestral de jun/2025 é consolidado [F]].
3. **Nenhum número sem `page`.** Se não tem página, não entra.
4. **Janela histórica: 2021–2025 (5 exercícios).** Não recuar para antes de 2020 na v0.1 (risco IAS 29/hiperinflação [NV] e mais reexpressões).

**Company ≠ Security.** Company é quem publica contas (SBA). Security é o que se negoceia (a ação SBA; um dia, obrigações do SBA). Na v0.1 há 1 company e 1 ação, e o que é de mercado vive na Company. **A Security torna-se necessária quando:** (a) o mesmo emitente tiver mais do que um título (ex.: ações + obrigações na BODIVA), (b) guardarmos séries diárias de preço/volume ou (c) calcularmos métricas por ação que dependam do nº de ações em datas diferentes.

---

## 7. Standard Bank MVP — lista fechada

| # | Feature | Prioridade | Porquê | Dependências | Esforço | Risco | Valida a hipótese? |
|---|---|---|---|---|---|---|---|
| 1 | Registo de documentos (R&C 2021–2025 + semestral 2025) | **P0** | Sem fonte não há nada | Download | 2 h | PDFs em falta | Sim |
| 2 | Dicionário de ~25 conceitos bancários | **P0** | Permite repetir no BAI | — | 3 h | Sobre-desenhar | Sim |
| 3 | `facts.csv` com ~40 rubricas × 5 anos, cada uma com página | **P0** | É o ativo | 1, 2 | 2 dias | Erros de escala e reexpressões | Sim |
| 4 | `validate.py` (somas, escala, sinais, anos) | **P0** | Credibilidade | 3 | 3 h | — | Sim |
| 5 | `metrics.py`: ~10 métricas | **P0** | Nível “Analisar” | 3 | 4 h | Fórmulas discutíveis (ex.: ROE médio vs final) | Sim |
| 6 | Página: Resumo + 4 números grandes | **P0** | Responde às perguntas 1–4 | 5 | 3 h | — | Sim |
| 7 | Cascata “Como ganha dinheiro” | **P0** | Pergunta 2 e 7; reutiliza a D5 | 5 | 4 h | — | Sim |
| 8 | Evolução em 5 anos (small multiples) | **P0** | Perguntas 5 e 6 | 5 | 4 h | — | Sim |
| 9 | Provenance clicável (painel: doc, página, rubrica, valor bruto, fórmula; link `PDF#page=N`) | **P0** | Pergunta 8, e é o diferenciador | 3, 5 | 1 dia | O link `#page` não funciona em todos os viewers | **Sim, é a tese** |
| 10 | Fontes e metodologia + “O que isto não diz” | **P0** | Honestidade | 1, 2 | 2 h | — | Sim |
| 11 | Tabela de métricas 5 anos com fórmula no tooltip | **P1** | Nível “Analisar” | 5 | 3 h | — | Parcial |
| 12 | Demonstrações resumidas as-reported (DR + balanço) | **P1** | Nível “Investigar” | 3 | 4 h | — | Parcial |
| 13 | Bloco de mercado: factos do IPO | **P1** | Contexto 30/09 | fonte CMC/prospeto | 1 h | Dados de preço pós-IPO | Não |
| 14 | Responsivo a 375 px | **P1** | O público vem do Instagram | 6–10 | 4 h | — | Sim (indiretamente) |

**Métricas P0 (10):** Margem financeira · Produto bancário · Resultado líquido · Crescimento do RL (%) · ROE · ROA · Cost-to-income · Custo do risco (imparidades de crédito / crédito médio) · Rácio de transformação (crédito/depósitos) · Peso da margem financeira no produto bancário. Solvabilidade só se estiver publicada tal como reportada (não se calcula).

---

## 8. UX Architecture — Company Page v0.1

Ordem das secções = ordem das 8 perguntas do teste:

| # | Secção | Nível | Pergunta respondida | Conteúdo |
|---|---|---|---|---|
| 0 | **Cabeçalho** | — | — | Standard Bank Angola · Banca · Cotada na BODIVA desde 30/09/2026 · pílula “Exercício 2025 · Consolidado” |
| 1 | **Em resumo** | Entender | 1, 3, 4 | 2–3 frases factuais (o que é, dimensão) + **4 números grandes**: Produto bancário, Resultado líquido, ROE, Ativo total, cada um com Δ vs 2024 e ícone de fonte |
| 2 | **Como ganha dinheiro** | Entender | 2, 7 | Cascata: juros recebidos → juros pagos → **margem financeira** → + comissões → + cambiais/títulos → − custos → − imparidades → − impostos → **lucro**, com 1 frase de leitura |
| 3 | **Como está a evoluir** | Entender → Analisar | 5, 6 | 4 small multiples (RL, Produto bancário, ROE, Cost-to-income), 2021–2025, cada um com 1 frase de leitura |
| 4 | **Rentabilidade, eficiência e risco** | Analisar | 6, 7 | Tabela 10 métricas × 5 anos; tooltip com a fórmula; clique → provenance |
| 5 | **Mercado** | Analisar | — | Factos do IPO (preço de oferta, nº de ações, % dispersão). Sem valuation. “Dados de negociação: ainda sem histórico suficiente” |
| 6 | **Demonstrações** (recolhida por defeito) | Investigar | 8 | DR e balanço resumidos, rubricas originais, cada célula → página |
| 7 | **Fontes e metodologia** | Investigar | 8 | Documentos com link, dicionário de conceitos, fórmulas, “O que isto não diz”, data de atualização |

**Porque esta ordem:** o utilizador vem de uma peça social a perguntar “como é que este banco ganha dinheiro?”. A resposta tem de estar acima da dobra (secções 1–2). O rigor não desaparece, desce para as secções 6–7 e está **sempre a um clique** a partir de qualquer número (provenance). As 7 secções do briefing foram fundidas: *Business* entra no Resumo e *Financials + Performance* ficam divididos em Evolução, Métricas e Demonstrações. Com um só banco e 5 anos, 7 separadores seriam páginas vazias.

**Sem separadores (tabs) na v0.1:** uma única página com scroll e índice lateral. Os separadores escondem conteúdo, e o teste mede se a pessoa encontra as coisas.

**Componentes (6, e só estes):** `NumberBlock` · `Waterfall` · `SmallMultiple` · `MetricTable` · `SourceLink` + `ProvenancePanel` · `Reading` (frase de leitura) + `NotSays` (caixa accent-tint).
**Tokens:** os do Visual System v0.1 (D1), já decididos: paper #F5F1E8, ink #14161A, Petróleo #0E5A63, Source Serif 4 + Inter tabular. Verde/vermelho **só** em Δ.

---

## 9. Visual Development Method — *data-first, ugly-first*

**Recomendação: G = combinação, por esta ordem:** dados reais → HTML com Claude Code → teste → componente. **Figma/Canva: não para o produto** (Canva continua a ser para as peças sociais). **Screenshots de concorrentes: só como referência de padrão, nunca como spec.**

**Ciclo por componente (timebox de 90 min até ao 1.º teste):**
```
1. Pergunta       — que pergunta do teste este componente responde? (se nenhuma → não se constrói)
2. Contrato       — forma do JSON que recebe (ex.: Waterfall = [{label, value, type, fact_ids}])
3. Dados reais    — as linhas reais do SBA, já em facts.csv (nunca lorem ipsum, nunca [X])
4. Feio primeiro  — Claude Code gera HTML/SVG com os tokens já aplicados; sem polir
5. Teste 5 min    — 1 pessoa, 1 pergunta, cronometrado; anota onde hesitou
6. Corrigir       — só o que o teste revelou
7. Congelar       — o componente vai para /components; muda-se só com novo teste
8. Reutilizar     — BAI entra com o mesmo componente; se precisar de mudar, o componente estava mal
```

**Regras anti-polimento:**
- **Nada é polido antes de passar num teste com dados reais.**
- **Máximo de 2 iterações visuais por componente antes do teste** (se passar disso, está-se a polir).
- **Os tokens já estão decididos** (D1), e por isso o “bonito mínimo” é gratuito. Não se reabre tipografia nem cor.
- **Um prompt de Claude Code = um componente + um ficheiro de dados.** Nunca “desenha-me a página inteira”.
- **Critério de congelar:** a pessoa de teste responde à pergunta do componente sem ajuda.

**Porque não Figma:** o risco da Kuzela não é visual, é de dados (escala, reexpressões, rubricas que não encaixam). O Figma com números falsos esconde exatamente esse risco. O HTML com dados reais expõe-no no 1.º dia.

---

## 10. Sprint de 14 dias (23 set → 6 out 2026)

| Dia | Data | Objetivo | Output | NÃO fazer |
|---|---|---|---|---|
| 0 | 22/09 | Auditoria de 30 min do EBITDA Software + Kitadinvest (§3.4) | 5 respostas + screenshots | Redesenhar a estratégia |
| 1 | 23/09 | Obter documentos SBA 2021–2025 (+ semestral 2025) | `documents.csv` + PDFs + decisão consolidado/individual | Procurar anos antes de 2021 |
| 2 | 24/09 | Dicionário de conceitos + contrato de dados | `concepts.csv` (~25), esquema `facts.csv` | Taxonomia genérica para todos os setores |
| 3 | 25/09 | Extrair FY2025 (DR + balanço) com página | ~40 linhas verificadas | Automatizar a extração |
| 4 | 26/09 | Extrair FY2021–2024 + `validate.py` | ~200 linhas; relatório de validação a verde | Corrigir “à mão” sem registar reexpressões |
| 5 | 27/09 | `metrics.py` → `metrics.json` | 10 métricas × 5 anos com fórmula | Valuation, rácios de mercado |
| 6 | 28/09 | Página feia v0: Resumo + Cascata com dados reais | `index.html` (secções 1–2) | Tipografia fina, animações |
| 7 | 29/09 | **Teste #1** (3 pessoas, perguntas 1–4 e 7) + fechar dados da CGD-01 | Notas de teste; JSON da peça de 30/09 | Adiar a peça por causa da página |
| 8 | 30/09 | Publicar CGD-01 (dia da estreia na BODIVA); secções 3–4 da página | Peça publicada; small multiples + tabela | Misturar dados de negociação do 1.º dia |
| 9 | 01/10 | Provenance: `SourceLink` + `ProvenancePanel` + `PDF#page=N` | Qualquer número → documento/página | Visualizador de PDF embutido |
| 10 | 02/10 | Secções 5–7 (mercado, demonstrações, fontes/metodologia) | Página completa | Separadores, pesquisa, login |
| 11 | 03/10 | Passagem a 375 px + tokens | Página responsiva | Dark mode, PWA |
| 12 | 04/10 | **Teste #2** (5 pessoas: 2 investidores novos, 1 jornalista, 1 estudante de finanças, 1 bancário) | Tempo por pergunta; nº de pessoas que abriram a fonte | Explicar a página durante o teste |
| 13 | 05/10 | Corrigir só o que o teste #2 mostrou; congelar componentes | `/components` congelado | Features novas |
| 14 | 06/10 | Publicar + DoD + decisão go/no-go para o BAI (CGD-01 de 21/10) | **Standard Bank Company Page V0.1 funcional** + 1 página de aprendizagens | Começar o BAI no mesmo dia |

---

## 11. MVP Definition of Done

- [ ] 100% dos números vêm de `facts.csv`; **zero números escritos no HTML**
- [ ] Todos os factos têm `doc_id` + `page` + `label_original` + `verified_by`
- [ ] 5 exercícios (2021–2025), uma só base (consolidado **ou** individual), escala normalizada
- [ ] `validate.py` passa: as parcelas da cascata somam ao lucro reportado (tolerância de arredondamento documentada)
- [ ] 10 métricas com fórmula visível
- [ ] Qualquer número → painel de provenance → link para a página do PDF oficial
- [ ] Secções 0–7 presentes, cada gráfico com 1 frase de leitura, “O que isto não diz” presente
- [ ] Legível a 375 px, sem scroll horizontal
- [ ] Build reproduzível: `python build.py` gera a página a partir dos CSV
- [ ] **Teste #2:** ≥ 4 de 5 pessoas respondem às 8 perguntas em ≤ 5 min; ≥ 3 de 5 abrem uma fonte sem que lhes seja pedido
- [ ] Sem BUY/SELL, scores, gauges, preço-alvo nem valuation
- [ ] Publicada num URL partilhável

---

## 12. Kill List (não construir agora)

Login/contas · base de dados · backend/API · pesquisa · screener · heatmap · cotações em tempo real · alertas · watchlist/portfólio · valuation (DCF, múltiplos, fair value) · scores/ratings · copiloto IA/chat · extração automática de PDF · comparador de empresas · páginas de outras empresas antes do go/no-go · dados trimestrais · dark mode · separadores · animações · visualizador de PDF embutido · design system em Figma · componentes React/Next.js · newsletter/paywall · modelo de negócio · Security/Event como entidades · segmentos detalhados · dados de negociação diária · tradução EN · SEO.

---

## 13. Next 3 Builds

1. **Primeiro:** `documents.csv` + `concepts.csv` + `facts.csv` do Standard Bank 2021–2025, validados e com página (dias 1–5). Servem a CGD-01 de 30/09 **e** a company page.
2. **Depois:** a company page feia com dados reais: Resumo → Cascata → Evolução → Provenance, testada com pessoas (dias 6–10).
3. **Em terceiro:** repetir no **BAI** com os mesmos componentes, sem mudar layout. Se o BAI entrar só com dados novos, o sistema funciona (teste do template mestre, MOCK 2).

---


## Fontes principais (consultadas via pesquisa web a 22/09/2026)

- EBITDA Software: https://www.ebitdasoftware.com/ (descrição indexada; site bloqueado no proxy)
- Kitadinvest: https://kitadinvest.com/stocks/BFA
- Standard Bank IPO: https://allafrica.com/stories/202609080013.html · https://www.riotimesonline.com/standard-bank-angola-bodiva-offer-2026/ · https://www.businessday.co.za/world/international-companies/2026-09-06-angola-regulator-backs-sale-of-34-stake-in-standard-bank-unit/
- Relatórios SBA: https://www.standardbank.co.ao/angola/pt/Rela%C3%A7%C3%B5es-com-Investidores/Informa%C3%A7%C3%A3o-Financeira/Relatorio-e-contas
- Unitel IPO: https://eco.sapo.pt/2026/07/27/procura-de-acoes-da-unitel-supera-oferta-em-120-e-estado-angolano-encaixa-quase-300-milhoes/ · https://www.correiodamanhacanada.com/bolsa-de-valores-angolana-destacou-entrada-de-novos-11-264-acionistas-da-unitel/
- Cotadas e dividendos: https://lidermagazine.ao/cinco-empresas-cotadas-na-bodiva-distribuem-dividendos-em-abril/ · https://expansao.co.ao/empresas/detalhe/bai-bfa-bcga-ensa-e-bodiva-pagam-dividendos-recorde-de-344-milhoes-usd-ja-na-proxima-semana-71026.html
- BODIVA: https://www.bodiva.ao/estatistica · https://www.bodiva.ao/simulador · https://carteira.bodiva.ao/
- CMC divulgação: https://www.cmc.ao/sites/default/files/2025-06/Projecto%20de%20Regulamento%20sobre%20os%20Deveres%20de%20Informa%C3%A7%C3%A3o%20dos%20Emitentes_060625.pdf · https://www.cmc.ao/sites/default/files/2024-08/INSTRU%C3%87%C3%83O%20N%C2%BA%2002-CMC-03-23-Presta%C3%A7%C3%A3o%20de%20Informa%C3%A7%C3%A3o%20pelos%20Emitentes.pdf
- Fiscal.ai: https://fiscal.ai/pricing/ · https://www.findmymoat.com/tools/fiscal-ai
- Stock Simplifier: https://stocksimplifier.com/ · https://terminalvalue.io/tool/stock-simplifier
- ValueSense: https://valuesense.io/ · https://comparecamp.com/value-sense-review/
- Koyfin/TIKR: https://www.findmymoat.com/vs/koyfin-vs-tikr · https://quantroutine.com/tools/tikr/
- Simply Wall St: https://support.simplywall.st/hc/en-us/articles/360001740916-How-does-the-Snowflake-work · https://www.findmymoat.com/tools/simply-wall-st
- Finviz: https://www.stockbrokers.com/review/tools/finviz · TradingView: https://www.tradingview.com/support/solutions/43000759574-introduction-to-fundamental-analysis-on-tradingview/
- AlphaSense: https://www.alpha-sense.com/pricing/ · https://www.spendhound.com/marketplace/alphasense-pricing
- Stears: https://www.readcommunique.com/p/stears-pivot-african-new-media-gap
- FT Visual Vocabulary: https://github.com/Financial-Times/chart-doctor/tree/main/visual-vocabulary
