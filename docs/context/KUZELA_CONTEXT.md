# KUZELA — Contexto do projeto (vindo do Notion)

**Cópia de 24 set 2026, a partir das páginas do Notion editadas a 21 set 2026.**
O Notion continua a ser a **casa oficial** da estratégia, da parte editorial e da parte visual. Este ficheiro existe para que os agentes trabalhem sem acesso ao Notion. Se houver diferença entre os dois, manda o Notion: corrige-se aqui.
Para o produto de dados (company page, pipeline), a casa oficial é o repositório: `docs/plan/` e `docs/strategy/`.

| Tema | Página Notion (casa oficial) |
|---|---|
| Mapa geral | [00 — START HERE](https://app.notion.com/p/3e10171f3d25817d9ac1f6b6337eb755) |
| Posicionamento | [Positioning](https://app.notion.com/p/3e20171f3d2581c7b85febb47d97e320) |
| Audiência | [Audience](https://app.notion.com/p/3e20171f3d2581208539ce474f3e5497) |
| Hipóteses e decisões | [Strategic Decisions](https://app.notion.com/p/3e20171f3d2581f58417fb2c1aa3ae41) |
| Identidade e voz | [Kuzela](https://app.notion.com/p/3e20171f3d2581c6af5df4dfa3cb065d) |
| Séries e formatos | [Content Format Architecture](https://app.notion.com/p/3e10171f3d2581f8a0a9e3ccbfff8cf9) |
| Tokens visuais | [Visual System](https://app.notion.com/p/3e00171f3d258112941dc6742d4fb19d) |
| Blocos T1–T5 | [Templates](https://app.notion.com/p/3e20171f3d258163ad4de7d2784a9b99) |
| Como se produz uma peça | [Guia de Execução](https://app.notion.com/p/3e20171f3d258173826cf1a4b1aaf935) |
| Estados, pauta, métricas | [Production Workflow](https://app.notion.com/p/3e20171f3d2581d39b85eb19eb0410f3) |
| Ferramentas | [Tools](https://app.notion.com/p/3e20171f3d25811d8913c758ed9ab0b8) |

---

## 1. O que é a Kuzela

**Kuzela Research** é uma publicação financeira que explica Angola através de dados, empresas, economia e mercados, de forma visual e compreensível. Está na **Fase 1**: publicar, testar e aprender. Não há produto pago, curso, subscrição nem research comercial.

- **Problema:** a informação existe, mas está em PDFs (relatórios e contas, BNA, INE, BODIVA) e em notícias com números soltos, sem contexto.
- **Diferenciação:** dados angolanos + visual + fonte + interpretação.
- **Promessa:** mostrar, com dados e fonte, como funcionam as empresas, a economia e o mercado de capitais angolanos, para que a pessoa perceba sozinha, sem ter de abrir um PDF.
- **O que não somos:** literacia financeira (isso é do Hugo), gráficos soltos ou dicas de investimento.
- **Lente:** Angola → África → Mundo. O mundo só entra quando ajuda a explicar Angola.
- **Evolução prevista:** Conteúdo → Educação → Análise → Research → Dados → Produtos. Na Fase 1 só se faz o primeiro passo.
- **Nome:** chamava-se KAIROS até 20 set 2026. Mudou só o nome; IDs, séries e tokens mantêm-se.

**Kuzela Data** é a camada de produto: a *company page* verificável (o primeiro caso é o Standard Bank Angola). Está descrita em `docs/strategy/` e `docs/plan/`.

## 2. Pessoas e papéis

| Quem | Função |
|---|---|
| **Uziel** | Dono do projeto e dos dados. Decide o modelo, os conceitos e as métricas, verifica cada número, escreve as leituras e aprova o que é publicado. Tem **cerca de 4 h por semana**. O perfil pessoal está parado. |
| **Hugo** | Atenção e distribuição ("Porque é que isto te importa?"). Faz a segunda leitura das peças com risco reputacional e recruta pessoas para os testes. |
| **Kuzela Research** | Conhecimento acumulado ("O que é que os dados mostram?"). É uma marca sem cara. |
| **Claude** | Constrói o software, extrai números dos PDFs e critica textos. **Nunca decide significado financeiro e nunca marca um número como verificado.** |

## 3. Audiência

Jovem profissional angolano, 22–35 anos, com curso superior. Ouviu falar do IPO do Standard Bank e da BODIVA e quer perceber empresas e economia sem ser da área de finanças.

**Teste de cada peça:** um licenciado que não é de finanças percebe a conclusão em 10 segundos? Um bancário não encontra nenhum erro? Se falha a primeira pergunta, a peça está densa demais. Se falha a segunda, não sai.

## 4. Voz

> **Clara, exata e neutra. Mostra, não opina.**
> Escrever como um bom gráfico do Financial Times: o título diz a conclusão, o gráfico prova-a e a fonte está em baixo.

- **Clara:** frases curtas; um termo técnico só aparece se for explicado na mesma linha.
- **Exata:** todos os números têm unidade, período e fonte.
- **Neutra:** sem primeira pessoa e sem adjetivos de opinião ("impressionante", "preocupante").
- **Didática:** cada gráfico tem uma frase de leitura.
- **Nunca:** recomendações de compra ou venda, "está cara/barata", previsões próprias, alarmismo, perguntas como título, autopromoção.
- **Título = conclusão**, com número sempre que houver um forte. ✗ "Inflação em Angola" → ✓ "Inflação abaixo de 10% pelo segundo mês".
- Todas as peças têm a secção **"O que isto não diz"**.

## 5. Regras dos números

- Português de Portugal: vírgula decimal e ponto nos milhares (4.533 mil milhões Kz). Escreve-se "mil milhões", nunca "bilhões".
- Kz por defeito. USD só se a fonte estiver em USD ou para comparar com outros países, sempre com a taxa de câmbio e a data.
- Percentagens com 1 casa decimal. Distinguir **%** de **p.p.**
- **Nominal vs real:** indicar "em termos nominais" sempre que a diferença importe.
- Dizer sempre a base da variação (face a 2024, face ao mês anterior).
- **Fontes que se podem usar:** R&C das empresas, BNA, INE, Ministério das Finanças, BODIVA, CMC (prospetos), FMI, Banco Mundial, Deloitte/KPMG (banca), BFA Estudos Económicos.
- **Só como contexto:** Expansão, Valor Económico, Forbes África Lusófona, Economia & Mercado, agências.
- **Nunca:** redes sociais, blogs sem fonte, Wikipédia, respostas de IA sem o documento aberto.
- **Verificação antes de publicar:** número visto no original com página anotada; unidade e escala certas; período certo; base (consolidado/individual) igual em toda a peça; as partes somam ao total; comparações só entre métricas com a mesma definição.
- **Um número que não passa sai da peça, ou a peça é adiada.** Não se escreve "cerca de" para esconder uma dúvida.
- **Erro publicado:** corrige-se em 24 h com um comentário fixado. Nunca se apaga em silêncio.

## 6. Séries e formatos

A Kuzela Research publica **apenas 3 séries** (regra 80/20: 80% nas séries, 20% em testes).

| Território | Série | Pergunta | Formatos |
|---|---|---|---|
| Empresas | **Como Ganha Dinheiro** | Como é que esta empresa transforma a atividade em lucro? | CGD-01 (one-pager master), CGD-02 (carrossel mobile) |
| Mercados | **O Mercado** | O que está a acontecer no mercado? | MK-01 (3–4 gráficos), EX-01 (explainer) |
| Economia | **Angola em Números** | O que é que os dados mostram? | AIN-01 (1 visual), AIN-02 (mini data story) |
| Transversal | — | Como se comparam? | CMP-01 (só com a mesma métrica, unidade, período e base) |
| Fase 2 | — | — | EQ-01 (company overview), MD-01 (deep dive) |

**Regra:** as empresas são dados e não templates. Não existe "template BAI"; existe o CGD-01, que recebe qualquer empresa.

**Motor económico de um banco (cascata do CGD-01):** juros recebidos − juros pagos = margem financeira; + comissões; + resultados cambiais e de títulos; − custos; − imparidades; − impostos = lucro.

**Blocos de slide (T1–T5):**
| Bloco | Uso |
|---|---|
| T1 Capa | Tag → H1-conclusão → hero number → período |
| T2 Número + Leitura | Um KPI lido em 2 s, com delta e fonte |
| T3 Gráfico + Leitura | H2-conclusão → gráfico (≈60% da altura) → leitura |
| T4 Lado a lado | A vs B, mesma métrica e escala |
| T5 Fecho | "O que isto não diz" → fontes → glossário → "Guarda e segue" |

CGD-01 = T1 + T2×4 + T3 · CGD-02 = T1·T3·T2·T2·T5 · MK-01 = T1·T3×3–4·T5 · AIN-02 = T1·T3·T2·T5 · CMP-01 = T1·T4·T5.

## 7. Visual System v0.1 (tokens)

| Token | HEX | Uso |
|---|---|---|
| paper | `#F5F1E8` | Fundo |
| ink | `#14161A` | Texto, números, série de comparação |
| accent (Petróleo) | `#0E5A63` | A única cor da marca e a série em destaque |
| accent-tint | `#D7E5E3` | Caixa "O que isto não diz", áreas de gráfico |
| grey-context | `#A8A396` | Séries que não estão em destaque |
| rule | `#DCD5C6` | Linhas, eixos, separadores |
| up / down | `#2E7D4F` / `#B3402F` | **Só** em variações (▲ ▼) |

- **Tipos de letra:** Source Serif 4 nos títulos (alternativas: Newsreader, IBM Plex Serif) e Inter com algarismos tabulares nos números e no texto.
- **Canvas social:** 1080×1350, margens de 80 px, 6 colunas, espaçamentos múltiplos de 8, nada abaixo de 24 px.
- **Gráficos:** no máximo 3 cores (accent → + ink → + grey), sem grelha de fundo, rótulos nas barras, uma anotação, barras a começar no zero, sem 3D nem gradientes.
- **Cascata por defeito**; Sankey só com receitas genuinamente paralelas (D5).
- **Componentes:** Series tag, Period pill, Headline, Number, Leitura (barra accent de 6 px), Chart, Não diz, Source ("Fonte: BAI, Relatório e Contas 2025, p. 47"), Wordmark (KUZELA RESEARCH em Inter Bold), Pagination.
- **Nunca** usar a cor da empresa analisada. Sem dashboards, tickers, semáforos, scores nem recomendações.
- Se não se lê a 375 px, divide-se.

## 8. Hipóteses em teste

H1–H8 (Strategic Decisions): procura por conteúdo contextualizado, interesse além de finanças pessoais, conteúdo simples gera atenção, conteúdo profundo gera confiança, formatos diferentes têm funções diferentes, Hugo/Uziel com funções diferentes (em pausa), a Kuzela como ativo próprio, procura futura por produtos.

K1–K6 (testes da Kuzela): CGD de bancos gera saves acima da mediana · peças com timing batem as evergreen · O Mercado gera perguntas "como invisto?" · a conta ganha seguidores sem cara · título-conclusão com número bate título-tema · banca e fiscalidade despertam interesse.

**A métrica principal é saves por alcance.** Uma peça não valida nenhuma hipótese.

## 9. Decisões e pendentes

- **19 set:** contas próprias (IG, X, LinkedIn, Facebook); 1 peça por semana; começa com o Standard Bank (estreia na BODIVA a **30/09/2026**).
- **D1–D7** (fonte visual única, CGD-01 como master, EQ-01 fora da pauta, blocos vs receitas, cascata por defeito, sem componentes em código, gráficos gerados por código). Estão no decision log local `OUTPUTS/kuzela_research/KUZELA_RESEARCH_TEMPLATE_DECISIONS.md`, que ainda não está no repositório. A D3 e a D6 foram revistas pela estratégia de 22/09 para permitir **uma** company page estática.
- **D8–D13:** em `docs/plan/` §10.
- **Pendentes:** OD-1 (Kuzela = ecossistema ou camada editorial) · OD-4 (logótipo da empresa na capa; proposta: 96 px em ink) · OD-6 (Source Serif 4 existe no Canva?) · OD-7 (quem produz quando o Uziel não pode).

## 10. Pauta de arranque

| Data | Peça | Série | Formato |
|---|---|---|---|
| 30/09 | Standard Bank Angola — de onde vem o lucro | Como Ganha Dinheiro | CGD-01 + CGD-02 |
| 06/10 | O Standard Bank na BODIVA: a primeira semana | O Mercado | MK-01 |
| 16/10 | O Kwanza em dados | Angola em Números | AIN-02 |
| 21/10 | BAI — de onde vem o lucro | Como Ganha Dinheiro | CGD-01 + CGD-02 |
| 29/10 | BODIVA em números | O Mercado | MK-01 |

A pauta em vigor está no [Editorial Calendar](https://app.notion.com/p/3e20171f3d25817cb9f9c0e18b313585).

## 11. Ferramentas existentes (fora do repositório)

Estas ferramentas vivem localmente no computador do Uziel, em `projetos/BOD_bodiva/OUTPUTS/kuzela_research/`, e **ainda não estão no git**:
- `tools/kuzela_research_charts.py`: JSON de rubricas → SVG 1080×1350 com os tokens (cascata).
- `tools/sankey_builder.html` + `tools/importador.js`: Sankey no browser; `reconciliar()` identifica subtotais pela soma.
- `templates/specs/`: specs de AIN-01, AIN-02, CGD-01, EQ-01, EX-01 e MD-01 (faltam CGD-02, MK-01 e CMP-01).

**Pipeline de uma peça social:** Fonte (PDF) → Folha de Dados → JSON → gerador → SVG/PNG → Canva.
