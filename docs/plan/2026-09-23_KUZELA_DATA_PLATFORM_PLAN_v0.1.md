# KUZELA DATA — Plano completo v0.1
**Da arquitetura à construção do software · 23 set 2026**

> Este plano junta as duas arquiteturas (dados e software), os processos, a organização da equipa e a ordem de construção.
> Âmbito: **Standard Bank Angola (SBA), 5 exercícios (2021–2025), uma company page.**
> Complementa `docs/strategy/2026-09-22_KUZELA_MVP_STRATEGY.md` (mercado, posicionamento, UX). Não repete essa parte.

**Legenda:** **[D]** decisão tomada neste plano · **[P]** decisão por defeito, reversível · **[?]** por confirmar com o documento aberto

---

## 0. Princípios que governam tudo

1. **A fonte manda.** O PDF oficial é a verdade. A IA ajuda a lê-lo, mas nunca o substitui.
2. **O que foi lido nunca se altera.** O que foi lido (RawFact) e o que a Kuzela conclui (Fact) vivem em camadas separadas.
3. **Um número por uma casa.** Existe no máximo um Fact por *empresa × conceito × período × base*.
4. **Os humanos editam as entradas e as máquinas geram as saídas.** As pessoas escrevem documentos, leituras e mapeamentos. Os scripts geram facts, métricas e a página. Ninguém edita um ficheiro gerado.
5. **Tudo é rastreável para trás.** Página → métrica → facts → raw facts → documento → página do PDF.
6. **Mudamos de tecnologia por necessidade, não por ambição.** Cada mudança de fase tem um gatilho objetivo (§9).

As 4 perguntas que o dono dos dados tem de saber responder para cada número:

| Pergunta | Nome técnico | Onde fica a resposta |
|---|---|---|
| O que é este dado? | Definition | `concepts.csv` |
| De onde veio? | Provenance | `raw_facts.csv` (documento + página + rubrica) |
| Como foi transformado? | Transformation | `concept_map.csv` + regra de seleção no Fact |
| O que se calcula a partir dele? | Analytics | `metrics.py` (fórmula + inputs) |

---

## 1. Arquitetura

### 1.1 Arquitetura de dados — o que acontece ao número

```
DOCUMENT          PDF oficial, registado e com impressão digital (sha256)
   ↓
RAW FACT          célula lida: rubrica original, valor impresso, escala, página, coluna
   ↓  (verificação humana)
CONCEPT MAP       "esta rubrica, nesta tabela, desta empresa = NET_INCOME"
   ↓
FACT              valor Kuzela normalizado (Kz, sinal Kuzela), aponta para 1 raw fact
   ↓
METRIC            cálculo sobre facts (ROE, cost-to-income…), guarda os facts usados
   ↓
INSIGHT           frase escrita por humano, aponta para métricas/facts
   ↓
PRODUTO           company page (e peças editoriais CGD-01)
```

### 1.2 Arquitetura de software — onde as coisas vivem

| Caixa | Arquitetura final | **v0.1 (agora)** | Porquê na v0.1 |
|---|---|---|---|
| **Storage** | Object storage (S3/R2) | PDFs em pasta local + cópia no Google Drive; `sources/manifest.csv` com URL oficial e sha256 | PDFs pesam 10–20 MB; não vão para git |
| **Pipeline** | Jobs orquestrados | Scripts Python corridos à mão (`make build`) | 1 empresa, atualização rara |
| **Database** | PostgreSQL | **Ficheiros CSV (entradas) + JSON (saídas) em git** | ~300 linhas; git dá histórico, diffs e revisão gratuitos |
| **API** | API REST (`GET /companies/sba`) | **Ficheiro `site/data/sba.json` gerado no build** | Página só de leitura; o JSON é o contrato que a API terá depois |
| **Frontend** | App web | **HTML estático** + JS mínimo, alojado no GitHub Pages | Sem servidor, sem custos, URL partilhável |

**[D] O modelo de dados é o mesmo nas duas colunas.** Passar para PostgreSQL + API (Fase C, §9) é escrever um script de carga e um servidor que devolve o **mesmo JSON**. Não se redesenha nada.

### 1.3 As duas arquiteturas encaixadas

```
SOFTWARE                          DADOS
────────────────                  ────────────────
sources/ (PDF)          ─────►    DOCUMENT
data/input/*.csv        ─────►    RAW FACT · CONCEPT · CONCEPT MAP
pipeline/normalize.py   ─────►    FACT
pipeline/metrics.py     ─────►    METRIC
data/input/insights.csv ─────►    INSIGHT
pipeline/build.py       ─────►    site/data/sba.json  (o "API")
site/                   ─────►    COMPANY PAGE
```

---

## 2. Modelo de dados v0.1

### 2.1 Mapa de relações

```
COMPANY 1───n DOCUMENT 1───n RAW_FACT n───1 CONCEPT_MAP ───► CONCEPT
   │                              │
   │                              └──(escolhido por)──► FACT n───n METRIC_VALUE ◄── METRIC (definição em código)
   │                                                     │
   └────────────────────────────────────────────────────┴──► INSIGHT (aponta para facts/métricas)
MARKET_FACT (factos do IPO) ──► COMPANY, DOCUMENT
```

### 2.2 Ficheiros editados por humanos (`data/input/`)

**`companies.csv`**
| Campo | Exemplo | Regra |
|---|---|---|
| company_id | `SBA` | Código interno curto, maiúsculas, imutável |
| name | Standard Bank Angola | |
| legal_name | Standard Bank de Angola, S.A. | [?] confirmar no R&C |
| sector / industry | Serviços financeiros / Banca | |
| country | AO | |
| listing_date | 2026-09-30 | |
| ir_url | URL da página de Relações com Investidores | |
| description_pt | 2–3 frases factuais | Escrita por humano, com fonte |

**`documents.csv`**
| Campo | Exemplo | Regra |
|---|---|---|
| document_id | `SBA_RC_2025_ANUAL_PT` | `{empresa}_{tipo}_{ano}_{periodo}_{idioma}` |
| company_id | `SBA` | |
| doc_type | `RC_ANUAL` / `RC_SEMESTRAL` / `PROSPETO` / `COMUNICADO` | |
| period_end | 2025-12-31 | |
| basis | `consolidado` / `individual` / `ambos` | |
| language | `PT` | [P] PT é canónico |
| published_at | data de publicação | [?] |
| official_url | URL oficial | |
| sha256 | impressão digital do PDF | Gerado por `fetch_sources.py` |
| pdf_pages | nº de páginas | Gerado |
| local_path | `sources/SBA/SBA_RC_2025_ANUAL_PT.pdf` | |

**`raw_facts.csv`** — *o que está escrito no documento; uma linha por célula lida*
| Campo | Exemplo | Regra |
|---|---|---|
| raw_fact_id | `SBA_RC_2025_ANUAL_PT_p083_012` | `{document_id}_p{página PDF}_{sequência}` |
| document_id | `SBA_RC_2025_ANUAL_PT` | tem de existir em `documents.csv` |
| pdf_page | 83 | página do **ficheiro** (usada no link `#page=`) |
| printed_page | 81 | página impressa na folha (usada na citação) |
| statement | `DR` / `BAL` / `NOTA` / `OUTRO` | DR = demonstração de resultados; BAL = balanço |
| table_title | Demonstração dos Resultados Consolidados | tal como impresso |
| label_original | Resultado líquido do exercício | **tal como impresso, sem corrigir** |
| column_original | 31.12.2025 | tal como impresso (ex.: “2024 Reexpresso”) |
| value_as_printed | `(1 234 567)` | texto exato, com parênteses/espaços |
| value | -1234567 | número interpretado (parênteses → negativo) |
| scale | `unidades` / `milhares` / `milhões` | como indicado no cabeçalho da tabela |
| currency | AOA | |
| basis | consolidado | |
| period_start | 2025-01-01 | vazio para rubricas de balanço (saldo numa data) |
| period_end | 2025-12-31 | |
| extracted_by | `claude` / `uziel` | |
| status | `extraido` → `verificado` / `rejeitado` | só `verificado` entra no pipeline |
| verified_by / verified_at | uziel / 2026-09-25 | obrigatórios se `verificado` |
| notes | | ex.: “nota 12 detalha” |

**`concepts.csv`** — *o dicionário Kuzela*
| Campo | Exemplo |
|---|---|
| concept | `NET_INCOME` |
| name_pt | Resultado líquido |
| definition_pt | Lucro do exercício do grupo, depois de impostos, antes da atribuição a minoritários |
| statement | DR |
| kind | `fluxo` (período) / `saldo` (data) |
| kuzela_sign | `+` receita/ativo · `−` custo |
| sector | banca |

**`concept_map.csv`** — *a normalização; é aqui que vive o julgamento*
| Campo | Exemplo | Regra |
|---|---|---|
| company_id | SBA | |
| statement | DR | |
| label_original | Resultado líquido do exercício | igual ao raw fact |
| document_id | *(vazio)* | vazio = vale para todos os documentos da empresa; preenchido = exceção só para esse documento |
| concept | NET_INCOME | |
| sign_flip | `false` | `true` se o documento imprime custos positivos |
| note | “Inclui minoritários” | obrigatório quando a equivalência não é óbvia |
| decided_by / decided_at | uziel / 2026-09-24 | |

> Porque o mapa é por **empresa + demonstração + rubrica**, e não uma lista global de sinónimos: “Resultado do exercício” e “Resultado atribuível aos accionistas” **não** são o mesmo conceito. O mapa obriga a decidir caso a caso, e o raw fica sempre lá para corrigir.

**`market_facts.csv`** — *só factos de mercado com fonte (v0.1: IPO)*
`market_fact_id, company_id, item (OFFER_PRICE_MIN, OFFER_PRICE_MAX, SHARES_OUTSTANDING, SHARES_OFFERED_PUBLIC…), value, unit, as_of, document_id, pdf_page`

**`insights.csv`** — *leituras escritas por humanos*
`insight_id, company_id, section (resumo/cascata/evolucao/…), text_pt, refs (fact_ids/metric_ids separados por ;), written_by, reviewed_by, status (rascunho/publicado)`

### 2.3 Ficheiros gerados por scripts (`data/output/`, nunca editar à mão)

**`facts.json`** — gerado por `normalize.py`
```json
{
  "fact_id": "SBA_NET_INCOME_FY2025_CONS",
  "company_id": "SBA",
  "concept": "NET_INCOME",
  "period": "FY2025",
  "basis": "consolidado",
  "value_aoa": 25000000000,
  "raw_fact_id": "SBA_RC_2025_ANUAL_PT_p083_012",
  "candidates": ["SBA_RC_2025_ANUAL_PT_p083_012"],
  "selection_rule": "documento_mais_recente",
  "is_restated": false
}
```
*(valores ilustrativos — nenhum número real entra sem o PDF aberto)*

**`metrics.json`** — gerado por `metrics.py`
```json
{
  "metric_id": "ROE",
  "company_id": "SBA",
  "period": "FY2025",
  "value": 0.214,
  "formula": "NET_INCOME / média(TOTAL_EQUITY fim 2024, TOTAL_EQUITY fim 2025)",
  "inputs": ["SBA_NET_INCOME_FY2025_CONS", "SBA_TOTAL_EQUITY_FY2024_CONS", "SBA_TOTAL_EQUITY_FY2025_CONS"]
}
```

**`validation_report.md`** e **`coverage_report.md`** — o que passou, falhou e falta.

**`site/data/sba.json`** — tudo o que a página precisa, num só ficheiro. **É o contrato da futura API.**

### 2.4 Regras de normalização [D]

| # | Regra |
|---|---|
| N1 | Só raw facts com `status = verificado` são candidatos |
| N2 | `value_aoa = value × escala × (−1 se sign_flip)` |
| N3 | Um período aparece em vários documentos (ano corrente + comparativo do ano seguinte) → **ganha o documento mais recente**; os outros ficam em `candidates` |
| N4 | Se o valor escolhido difere do original em mais de 0,5% → `is_restated = true` e aparece no relatório |
| N5 | Nunca se misturam bases: a página usa **só consolidado** [P] |
| N6 | Convenção de sinal Kuzela: **receitas e ativos +, custos −** [P] (a cascata soma diretamente) |
| N7 | Rubrica sem mapeamento → não gera fact e aparece no `coverage_report` (nunca é adivinhada) |

### 2.5 Dicionário de conceitos v0.1 (banca) [?] a confirmar contra o R&C 2025

**Demonstração de resultados (DR)**
| Conceito | Nome PT |
|---|---|
| INTEREST_INCOME | Juros e rendimentos similares |
| INTEREST_EXPENSE | Juros e encargos similares |
| NET_INTEREST_INCOME | Margem financeira |
| NET_FEE_INCOME | Resultados de serviços e comissões |
| FX_RESULTS | Resultados cambiais |
| SECURITIES_RESULTS | Resultados de ativos e passivos financeiros / títulos |
| OTHER_OPERATING_RESULTS | Outros resultados de exploração |
| OPERATING_INCOME | Produto bancário |
| STAFF_COSTS | Custos com pessoal |
| GENERAL_ADMIN_COSTS | Fornecimentos e serviços de terceiros |
| DEPRECIATION | Depreciações e amortizações |
| OPERATING_COSTS | Custos operacionais (total) |
| LOAN_IMPAIRMENT | Imparidade de crédito a clientes |
| OTHER_IMPAIRMENT_PROVISIONS | Outras imparidades e provisões |
| PROFIT_BEFORE_TAX | Resultado antes de impostos |
| INCOME_TAX | Impostos sobre lucros |
| NET_INCOME | Resultado líquido do exercício |
| NET_INCOME_OWNERS | Resultado atribuível aos acionistas |

**Balanço (BAL)**
| Conceito | Nome PT |
|---|---|
| TOTAL_ASSETS | Ativo total |
| CASH_CENTRAL_BANK | Caixa e disponibilidades no BNA |
| LOANS_TO_CUSTOMERS_NET | Crédito a clientes (líquido) |
| SECURITIES_PORTFOLIO | Carteira de títulos (total) |
| CUSTOMER_DEPOSITS | Depósitos de clientes |
| TOTAL_LIABILITIES | Passivo total |
| TOTAL_EQUITY | Capital próprio |

**Outros (reportados, não calculados)**
| Conceito | Nome PT |
|---|---|
| CAPITAL_ADEQUACY_RATIO | Rácio de solvabilidade (tal como reportado) |
| EMPLOYEES | Colaboradores |
| BRANCHES | Balcões |

### 2.6 Métricas v0.1 (10)

| metric_id | Nome | Fórmula | Nota |
|---|---|---|---|
| NET_INCOME_GROWTH | Crescimento do resultado líquido | NI(t) / NI(t−1) − 1 | nominal, em Kz |
| OPERATING_INCOME_GROWTH | Crescimento do produto bancário | OI(t) / OI(t−1) − 1 | nominal |
| ROE | Rentabilidade do capital próprio | NI / média(Equity t−1, t) | média, não final |
| ROA | Rentabilidade do ativo | NI / média(Assets t−1, t) | |
| COST_TO_INCOME | Eficiência | −OPERATING_COSTS / OPERATING_INCOME | menor = mais eficiente |
| NII_SHARE | Peso da margem financeira | NII / OPERATING_INCOME | |
| FEE_SHARE | Peso das comissões | NET_FEE_INCOME / OPERATING_INCOME | |
| COST_OF_RISK | Custo do risco | −LOAN_IMPAIRMENT / média(Loans net t−1, t) | com crédito líquido; nota de metodologia |
| LOANS_TO_DEPOSITS | Rácio de transformação | Loans net / Customer deposits | saldo fim de período |
| EQUITY_TO_ASSETS | Capital sobre ativo | Equity / Assets | alavancagem simples |

Regras: métricas com média precisam do ano anterior → 2021 só tem as métricas sem média (daí a extração incluir **2020 como comparativo** do R&C 2021, só para os saldos de balanço). **[D]** Nada de métricas em termos reais (inflação) na v0.1; a página diz “valores nominais”.

---

## 3. Processos

### 3.1 Pipeline de dados — passo a passo

| # | Passo | Entrada | Saída | Quem | Ferramenta | Verificação |
|---|---|---|---|---|---|---|
| P1 | **Ingerir** | URL oficial | PDF em `sources/`, linha em `documents.csv` | Uziel | `fetch_sources.py` | sha256 registado; nº de páginas |
| P2 | **Localizar** | PDF | lista de páginas com DR, balanço, notas-chave | Claude + Uziel | leitura do PDF | páginas anotadas em `documents.csv/notes` |
| P3 | **Extrair** | páginas | linhas `raw_facts.csv` com `status=extraido` | Claude (Code ou chat) | prompt padrão (§7.4) | formato validado pelo schema |
| P4 | **Verificar** | raw facts + PDF aberto | `status=verificado` ou `rejeitado` | **Uziel (sempre humano)** | PDF lado a lado com a folha | valor, escala, sinal, coluna, página |
| P5 | **Mapear** | rubricas novas | linhas `concept_map.csv` | Uziel decide; Claude sugere | — | nota obrigatória quando ambíguo |
| P6 | **Normalizar** | raw verificados + mapa | `facts.json` | script | `normalize.py` | regras N1–N7 |
| P7 | **Validar** | facts | `validation_report.md` | script | `validate.py` | regras V1–V12 (§4.1); falha bloqueia o build |
| P8 | **Calcular** | facts | `metrics.json` | script | `metrics.py` | cada métrica lista os inputs |
| P9 | **Interpretar** | métricas | `insights.csv` | Uziel escreve; Claude critica | — | cada frase com `refs`; voz Kuzela |
| P10 | **Publicar** | tudo | `site/` + tag de versão | script + Uziel aprova | `build.py` + GitHub Actions | CI verde; checklist de publicação |

### 3.2 Processos operacionais

**A. Entrar um documento novo (ex.: R&C semestral 2026)**
P1 → P2 → P3 → P4 → P5 (só rubricas novas) → `make build` → rever `validation_report` (reexpressões!) → P9 se mudar alguma leitura → P10.

**B. Corrigir um erro publicado (errata)**
1. Nunca se apaga nem se sobrescreve em silêncio.
2. Raw fact errado → `status=rejeitado` + nova linha correta (novo `raw_fact_id`).
3. Mapeamento errado → alterar `concept_map.csv` com nota e data.
4. `make build` → o diff de `facts.json` mostra exatamente o que mudou.
5. Entrada em `data/ERRATA.md`: data, número antigo, número novo, causa.
6. Nova versão de dados (patch: `data-v0.1.1`).

**C. Acrescentar uma empresa (ex.: BAI)**
1. Linha em `companies.csv`. 2. Processo A para cada documento. 3. Reutilizar `concepts.csv`; o `concept_map.csv` ganha linhas `company_id=BAI`. 4. **Teste do sistema:** se for preciso mudar o código ou os componentes para o BAI caber, regista-se em `docs/decisions/` porquê.

**D. Versionar dados**
`data-vMAIOR.MENOR.PATCH` — MAIOR: muda o modelo; MENOR: novo documento/empresa; PATCH: errata. A página mostra “Dados: v0.1.1 · atualizado a …”.

### 3.3 Ritmo semanal (depois do MVP)
| Quando | O quê | Duração |
|---|---|---|
| Quando sai um documento | Processo A | 2–4 h por R&C anual |
| Semanal (check-in) | rever `coverage_report` e ERRATA | 15 min |
| Mensal (review) | decisões de modelo pendentes; gatilhos de fase (§9) | 30 min |

---

## 4. Qualidade e governação

### 4.1 Regras de validação (`validate.py`)

| # | Regra | Falha = |
|---|---|---|
| V1 | Todos os ficheiros cumprem o schema (campos, tipos, valores permitidos) | bloqueia |
| V2 | IDs únicos; referências existem (document_id, company_id, concept) | bloqueia |
| V3 | `verificado` tem `verified_by` e `verified_at` | bloqueia |
| V4 | `pdf_page` ≤ `pdf_pages` do documento | bloqueia |
| V5 | Um único fact por empresa × conceito × período × base | bloqueia |
| V6 | INTEREST_INCOME + INTEREST_EXPENSE = NET_INTEREST_INCOME (± 1 unidade da escala) | bloqueia |
| V7 | Soma das parcelas do produto bancário = OPERATING_INCOME (±) | bloqueia |
| V8 | PROFIT_BEFORE_TAX + INCOME_TAX = NET_INCOME (±) | bloqueia |
| V9 | TOTAL_ASSETS = TOTAL_LIABILITIES + TOTAL_EQUITY (±) | bloqueia |
| V10 | Sinais coerentes com `kuzela_sign` | aviso |
| V11 | Variação anual > 100% em qualquer conceito | aviso (pode ser real — confirmar) |
| V12 | Reexpressões detetadas (N4) | aviso listado no relatório |

Tolerância de arredondamento: **1 unidade da escala impressa** (ex.: 1 milhar de Kz se a tabela está em milhares).

### 4.2 Estados e responsabilidade

```
raw fact:  extraido ──(Uziel confere no PDF)──► verificado ──► entra no pipeline
                     └───────────────────────► rejeitado  (fica guardado, com nota)
insight:   rascunho ──(2.ª leitura)──► publicado
```

### 4.3 Definição de “verificado”
Um raw fact só é `verificado` quando uma pessoa, com o PDF aberto na página, confirmou **cinco coisas**: valor, escala, sinal, coluna (ano certo) e rubrica. A IA nunca marca `verificado`.

---

## 5. Organização — quem faz o quê

### 5.1 Papéis

| Papel | Quem | Responsabilidade |
|---|---|---|
| **Data Product Owner / Financial Data Architect** | Uziel | Decide o modelo, os conceitos, os mapeamentos, as métricas e as regras; verifica cada número; escreve as leituras; aprova publicações |
| **Construtor de software** | Claude Code | Implementa schemas, scripts, testes, CI, página; nunca decide significado financeiro |
| **Assistente de extração** | Claude (Code ou chat) | Lê páginas do PDF e propõe raw facts; sugere mapeamentos; critica leituras |
| **Teste de compreensão** | Hugo + 3–5 pessoas recrutadas por ele | Testa se um leigo percebe a página; não mexe nos dados |

### 5.2 RACI resumido (R = faz, A = decide, C = consultado)

| Atividade | Uziel | Claude Code | Claude extração | Hugo |
|---|---|---|---|---|
| Modelo de dados e conceitos | A/R | C | C | — |
| Extração (P3) | A | — | R | — |
| Verificação (P4) | **A/R** | — | — | — |
| Mapeamento (P5) | A/R | — | C | — |
| Scripts, testes, CI, página | A | R | — | — |
| Leituras (P9) | A/R | — | C | C |
| Testes com utilizadores | A | — | — | R |
| Publicar | A | R | — | — |

### 5.3 Orçamento de tempo — atenção

A auditoria de 19 set dizia que o Uziel tem **~4 h/semana** para este projeto. O MVP completo custa cerca de **30–35 h do Uziel** (o trabalho do Claude Code não conta aqui):

| Bloco | Horas do Uziel |
|---|---|
| M0–M1 decisões, conceitos, revisão de setup | 4 |
| Extração + verificação (≈ 300 raw facts em 5 R&C) | 12–15 |
| Mapeamento | 2 |
| Rever métricas e validação | 2 |
| Leituras | 3 |
| Testes com pessoas | 3 |
| Revisões da página e publicação | 4 |

**[P] Consequência:** com 4 h/semana são **8–9 semanas**, não 14 dias. Opções: (a) aceitar 8–9 semanas; (b) blocos intensivos nos dias do sprint; (c) cortar para 3 exercícios (2023–2025), que corta ~40% da extração. **Recomendo (c) no primeiro ciclo** e acrescentar 2021–2022 depois de o sistema estar provado. **A prioridade absoluta é FY2025**, porque também alimenta a peça CGD-01 de 30/09.

---

## 6. Estrutura do repositório

```
Kuzela/
├── README.md                      como correr tudo em 3 comandos
├── Makefile                       make setup · make validate · make build · make test
├── pyproject.toml                 dependências Python
├── .github/workflows/
│   ├── validate.yml               corre validate + testes em cada push/PR
│   └── publish.yml                publica site/ no GitHub Pages ao criar tag data-v*
├── docs/
│   ├── strategy/                  estratégia (já existe)
│   ├── plan/                      este plano
│   ├── data/
│   │   ├── DATA_MODEL.md          versão viva da §2
│   │   ├── METHODOLOGY.md         fórmulas, convenções, o que os dados não dizem
│   │   └── EXTRACTION_GUIDE.md    como extrair e verificar (com o prompt padrão)
│   └── decisions/                 uma decisão por ficheiro (D8, D9…)
├── sources/                       PDFs — NÃO vão para git (.gitignore)
│   └── manifest.csv               URL + sha256 (vai para git)
├── data/
│   ├── input/                     EDITADO POR HUMANOS
│   │   ├── companies.csv
│   │   ├── documents.csv
│   │   ├── concepts.csv
│   │   ├── concept_map.csv
│   │   ├── raw_facts/SBA.csv      um ficheiro por empresa
│   │   ├── market_facts.csv
│   │   └── insights.csv
│   ├── output/                    GERADO — nunca editar
│   │   ├── facts.json
│   │   ├── metrics.json
│   │   ├── validation_report.md
│   │   └── coverage_report.md
│   └── ERRATA.md
├── pipeline/
│   ├── schemas.py                 definição dos objetos (a §2 em código)
│   ├── load.py                    lê e valida os CSV contra os schemas
│   ├── normalize.py               raw → facts (N1–N7)
│   ├── validate.py                V1–V12
│   ├── metrics.py                 10 métricas, cada uma com fórmula e inputs
│   ├── build.py                   gera site/data/sba.json + HTML
│   └── tools/
│       ├── fetch_sources.py       descarrega PDFs, calcula sha256
│       └── page_text.py           imprime o texto/tabelas de uma página (ajuda à extração)
├── site/
│   ├── templates/company.html     o molde da company page
│   ├── assets/                    CSS com os tokens do Visual System, JS mínimo
│   ├── data/                      gerado
│   └── index.html                 gerado
└── tests/
    ├── fixtures/                  mini-conjunto de dados fictícios para testes
    ├── test_normalize.py
    ├── test_validate.py
    └── test_metrics.py
```

**Regra de ouro para quem não programa:** tu só mexes em `data/input/`, `docs/` e aprovas pull requests. Tudo o resto é o Claude Code.

---

## 7. Construção do software

### 7.1 Stack [D]

| Peça | Escolha | Porquê | O que NÃO usar agora |
|---|---|---|---|
| Linguagem | **Python 3.12** | O gerador de gráficos da Kuzela já é Python; bom para dados e PDF | JavaScript no backend |
| Schemas | **Pydantic** | Define cada objeto uma vez e valida automaticamente | Base de dados |
| Leitura de CSV | biblioteca `csv` + Pydantic | Simples; ficheiros pequenos | pandas (desnecessário a esta escala) |
| Ajuda à extração | **pdfplumber** | Lê texto e tabelas por página | OCR, extração automática sem humano |
| Página | **Jinja2** → HTML estático + CSS + JS mínimo | Sem framework, sem build de frontend | React, Next.js, Astro |
| Gráficos | **SVG gerado em Python** (reutiliza a lógica de `kuzela_research_charts.py`) | Mesmo gráfico para a página e para o Canva | Bibliotecas de gráficos JS pesadas |
| Testes | **pytest** | Padrão | — |
| CI | **GitHub Actions** | Valida cada alteração automaticamente | — |
| Alojamento | **GitHub Pages** | Grátis, URL público, deploy por tag | Servidores, Vercel, bases de dados geridas |

### 7.2 Marcos de construção (cada um com critério de aceitação)

| Marco | O quê | Critério de aceitação | Horas Uziel | Depende de |
|---|---|---|---|---|
| **M0 · Esqueleto** | Estrutura de pastas, `Makefile`, `pyproject`, README, `.gitignore`, CI vazio a verde | `make setup && make test` corre sem erros | 0,5 | — |
| **M1 · Contrato de dados** | `schemas.py` + `load.py` + CSV vazios com cabeçalhos + `concepts.csv` preenchido + fixtures fictícias | CSV com erro de propósito → `make validate` diz qual linha e porquê | 2 | M0 |
| **M2 · Fontes** | `fetch_sources.py`, `manifest.csv`, `documents.csv` do SBA | PDFs descarregados, sha256 e nº de páginas preenchidos | 1 | M1 |
| **M3 · Extração FY2025** | Guia de extração + `page_text.py` + raw facts da DR e do balanço 2025 (com a coluna comparativa 2024) | ~60 raw facts `verificado`; alimenta a CGD-01 | 4 | M2 |
| **M4 · Normalizar e validar** | `concept_map.csv`, `normalize.py`, `validate.py`, relatórios | `facts.json` gerado; V1–V9 a verde; testes com fixtures | 2 | M3 |
| **M5 · Métricas** | `metrics.py` + testes | 10 métricas FY2025 (as que têm dados), cada uma com inputs | 1 | M4 |
| **M6 · Página feia** | `build.py`, template, secções Resumo + Cascata + provenance clicável | Qualquer número → painel com documento, página, rubrica, valor impresso, link PDF | 2 | M5 |
| **M7 · Histórico** | Extração dos restantes exercícios (2023–2024 no corte (c)) | Reexpressões detetadas e listadas; small multiples na página | 5 | M6 |
| **M8 · Página completa** | Secções evolução, métricas, mercado (IPO), demonstrações, fontes/metodologia; 375 px | Checklist da DoD da estratégia (§11) | 3 | M7 |
| **M9 · Teste e publicação** | Testes com 5 pessoas; correções; tag `data-v0.1.0`; GitHub Pages | URL público; ≥ 4/5 respondem às 8 perguntas em ≤ 5 min | 4 | M8 |

**M3 e M6 são os dois momentos de verdade.** M3 prova que os dados angolanos entram no modelo; M6 prova que a provenance funciona para um leigo. Se um dos dois falhar, para-se e revê-se o modelo antes de continuar.

### 7.3 Como dar instruções ao Claude Code — um marco de cada vez

Cada pedido segue este molde (nunca “constrói a plataforma”):

```
CONTEXTO: lê docs/plan/2026-09-23_KUZELA_DATA_PLATFORM_PLAN_v0.1.md, secção X.
TAREFA: implementa o marco Mn — [nome].
ENTRADAS: [ficheiros que já existem]
SAÍDAS: [ficheiros a criar]
ACEITAÇÃO: [critério da tabela 7.2]
NÃO FAZER: [lista do marco; ex.: não criar base de dados, não usar React]
NO FIM: corre make test e make validate, mostra o resultado, faz commit e push.
```

Exemplo real para M1:
> “Lê a §2 do plano. Implementa o M1: `pipeline/schemas.py` com Pydantic para Company, Document, RawFact, Concept, ConceptMap, MarketFact, Insight, exatamente com os campos e valores permitidos da §2.2; `pipeline/load.py` que lê `data/input/` e devolve erros com ficheiro + linha + campo; CSV vazios com cabeçalhos; `concepts.csv` preenchido com a §2.5; fixtures fictícias em `tests/fixtures/` com um erro de propósito. Não cries base de dados nem API. No fim corre `make test` e mostra.”

### 7.4 Prompt padrão de extração (P3)

```
Documento: {document_id}. Página PDF: {n} (página impressa: {m}).
Extrai TODAS as linhas da tabela "{título}" para o formato raw_facts.csv.
Regras:
- label_original e column_original exatamente como impressos, sem corrigir acentos nem abreviaturas;
- value_as_printed exatamente como está (parênteses, espaços);
- value: parênteses = negativo; não apliques a escala;
- scale: lê o cabeçalho da tabela ("milhares de Kwanzas" = milhares); se não houver, escreve "?" ;
- uma linha por célula (rubrica × coluna);
- status = extraido; extracted_by = claude;
- se uma célula estiver ilegível, não inventes: escreve ILEGIVEL em notes e deixa value vazio.
Devolve só o CSV.
```

Depois, **Uziel** abre a página e verifica linha a linha (P4). Sem exceções.

### 7.5 Estratégia de testes

| Nível | O quê | Onde |
|---|---|---|
| Schema | Cada ficheiro de entrada cumpre o modelo | `make validate` |
| Unidade | normalize (escala, sinal, reexpressão), métricas (fórmulas com números fictícios conhecidos) | `tests/` com fixtures |
| Contabilístico | V6–V9 sobre dados reais | `validation_report.md` |
| Regressão | Diff de `facts.json`/`metrics.json` entre versões | revisão do pull request |
| Humano | Verificação P4; teste de compreensão | fora do código |

### 7.6 Fluxo de trabalho em git (simples)
1. Um branch por marco (`m1-contrato-dados`, `m3-extracao-2025`…).
2. Pull request com CI a verde → Uziel revê **os diffs de `data/`** (é aí que está o risco) → merge.
3. Publicação só por tag `data-v*`.

---

## 8. Riscos técnicos e mitigação

| Risco | Probabilidade | Mitigação |
|---|---|---|
| Escala errada (milhares vs milhões) | Alta | `scale` obrigatório por linha; V6–V9 falham se a escala estiver trocada entre linhas |
| Reexpressões entre anos | Alta | N3/N4 + relatório; guardar sempre os dois valores |
| Rubricas mudam de nome entre anos | Alta | `concept_map` por rubrica; `coverage_report` mostra o que ficou por mapear |
| Consolidado vs individual misturados | Média | `basis` obrigatório; N5 |
| Tabela partida entre duas páginas | Média | `pdf_page` por linha (não por tabela) |
| PDF digitalizado (imagem, sem texto) | Baixa-média [?] | extração manual; OCR só se acontecer |
| URL oficial muda ou desaparece | Média | sha256 + cópia no Drive; espelho próprio na Fase B |
| Link `#page=N` não funciona no telemóvel | Média | mostrar sempre “p. N” em texto, além do link |
| Excesso de engenharia | Alta (perfil do projeto) | Gatilhos de fase (§9); lista “NÃO FAZER” em cada marco |
| Tempo do Uziel | Alta | corte (c) de 3 exercícios; FY2025 primeiro |

---

## 9. Evolução por fases (com gatilhos)

| Fase | Quando entra (gatilho) | O que muda | O que NÃO muda |
|---|---|---|---|
| **A · Ficheiros** (agora) | — | CSV/JSON + build estático; 1 empresa | — |
| **B · Várias empresas em ficheiros** | SBA v0.1 publicado + teste passou | BAI, BFA… com o mesmo pipeline; página de comparação setorial; espelho próprio dos PDFs | modelo, scripts |
| **C · Base de dados + API** | > 3 empresas **ou** perguntas que cruzam empresas **ou** > 1 pessoa a editar dados ao mesmo tempo | PostgreSQL (gerido, ex. Supabase/Neon); `load_db.py`; API só de leitura (FastAPI) que devolve **o mesmo JSON** de `site/data/` | modelo de dados, regras, processos |
| **D · Extração assistida em escala** | > 10 documentos/trimestre | Interface de verificação (PDF ao lado da linha); IA extrai primeiro, humano confirma | verificação humana obrigatória |
| **E · Mercado** | negociação regular das ações com histórico ≥ 6 meses | Entidade **Security**; séries diárias BODIVA; **Event** (dividendos, IPO) | Company continua a ser o objeto principal |

**Company ≠ Security:** entra na Fase E, ou antes se o mesmo emitente tiver ações e obrigações cotadas.

---

## 10. Decisões deste plano

| ID | Decisão | Estado |
|---|---|---|
| D8 | Duas camadas: RawFact (imutável) e Fact (gerado); normalização via `concept_map` por empresa + demonstração + rubrica | **[D]** |
| D9 | Fase A em ficheiros (CSV entrada / JSON saída) em git; PostgreSQL + API só com os gatilhos da Fase C | **[D]** |
| D10 | Base consolidada; sinal Kuzela (custos −); PT canónico; documento mais recente vence | **[P]** — confirmar ao abrir o R&C 2025 |
| D11 | Verificação sempre humana; a IA nunca marca `verificado` | **[D]** |
| D12 | Python + Pydantic + Jinja2 + SVG + GitHub Pages; sem frameworks de frontend | **[D]** |
| D13 | Primeiro ciclo com 3 exercícios (2023–2025) se o tempo for 4 h/semana | **[P]** — decide o Uziel |

Revisões às decisões anteriores: **D3 e D6** revistas para uma company page estática (ver estratégia de 22/09).

---

## 11. Primeira semana — checklist

- [ ] Confirmar D10 e D13 (5 min)
- [ ] Descarregar o R&C 2025 do SBA (PT) e anotar as páginas da DR e do balanço consolidados
- [ ] Claude Code: **M0** e **M1**
- [ ] Uziel: rever `concepts.csv` contra o R&C 2025 (retirar/acrescentar conceitos)
- [ ] Claude Code: **M2**
- [ ] Extrair e verificar a DR 2025 (**M3**, primeira metade) — alimenta a CGD-01 de 30/09

**A primeira coisa a pedir ao Claude Code:** “Implementa o M0 e o M1 do plano de 23/09.”
