# Kuzela — instruções para o Claude

A Kuzela Research é uma publicação financeira que explica Angola com dados, empresas, economia e mercados. Neste repositório constrói-se a **Kuzela Data**: a company page verificável, em que cada número se pode seguir até à página do PDF oficial. O primeiro caso é o **Standard Bank Angola (SBA)**, que começa a negociar na BODIVA a 30/09/2026.

Tudo se escreve em **português de Portugal**.

## Ler antes de trabalhar

| Ficheiro | O que tem | Quando ler |
|---|---|---|
| `docs/context/KUZELA_CONTEXT.md` | Posicionamento, audiência, voz, regras dos números, séries, Visual System (copiado do Notion) | Sempre, no início de uma tarefa |
| `docs/plan/2026-09-23_KUZELA_DATA_PLATFORM_PLAN_v0.1.md` | Modelo de dados, pipeline P1–P10, validação V1–V12, estrutura do repositório, stack, marcos M0–M9 | Qualquer tarefa de dados ou de software |
| `docs/strategy/2026-09-22_KUZELA_MVP_STRATEGY.md` | Concorrência, UX da company page, método visual, sprint, Definition of Done, kill list | Tarefas de produto ou de página |

Casa oficial: o **Notion** para a estratégia, a parte editorial e a parte visual; o **repositório** para os dados e o software. Não se duplica: aponta-se para a fonte.

## Estado atual (24 set 2026)

- Só existe documentação. Nenhum marco de código foi feito ainda.
- **Próximo passo:** M0 (esqueleto) e M1 (contrato de dados), ver plano §7.2.
- **Prioridade absoluta:** dados do FY2025 do SBA. Alimentam a peça CGD-01 de 30/09 e a company page.

## Regras que nunca se quebram

1. **A fonte manda.** Nenhum número entra sem documento, página e rubrica. Nunca se inventam, estimam ou "arredondam" números para preencher espaço. Os valores de exemplo são marcados como ilustrativos.
2. **A IA nunca marca `status=verificado`.** Só o Uziel verifica (P4). O Claude extrai sempre com `status=extraido`.
3. **O Claude nunca decide significado financeiro.** Conceitos, mapeamentos (`concept_map.csv`), fórmulas e leituras são decididos pelo Uziel. O Claude propõe e assinala as dúvidas.
4. **Ficheiros gerados não se editam à mão:** `data/output/`, `site/data/`, `site/index.html`.
5. **Os PDFs não vão para o git.** Só `sources/manifest.csv`, com o URL e o sha256.
6. **Stack fechado (D12):** Python 3.12, Pydantic, csv, pdfplumber, Jinja2, SVG gerado em Python, pytest, GitHub Actions, GitHub Pages. **Não usar:** base de dados, API, React, Next.js, pandas, Figma. Ver a kill list da estratégia (§12).
7. **Sem recomendações de investimento:** nada de BUY/SELL, scores, preço-alvo, valuation ou "está barata".
8. **Voz Kuzela:** clara, exata e neutra. O título diz a conclusão e cada número leva unidade, período e fonte.
9. **Tokens visuais:** os do Visual System (paper `#F5F1E8`, ink `#14161A`, Petróleo `#0E5A63`, Source Serif 4 + Inter tabular). Não se reabrem cores nem tipografia.
10. **Um marco de cada vez.** Nunca se pede ou se faz "constrói a plataforma inteira".

## Governança: a empresa Kuzela

O trabalho está organizado como uma empresa:

```
BOARD: Uziel
  define objetivos · aprova PRs (merge) · verifica números (P4) · decide o significado financeiro
        │
CEO: a sessão principal (este ficheiro)
  recebe o objetivo → planeia → delega → integra → apresenta ao board
        │
  ├── CTO (`cto`)                         software: marcos M0–M9, pipeline, testes, CI, company page
  ├── COO (`coo`)                         operações de dados: fontes, documentos, extração P1–P3, propostas de mapeamento P5
  ├── Diretor Editorial (`diretor-editorial`)  textos, leituras, peças CGD/MK/AIN, voz Kuzela
  └── Auditor (`auditor`)                 independente: dá parecer ao board sobre tudo o que é entregue
```

Nota técnica: os diretores não podem chamar outros agentes. É o CEO que coordena todos diretamente. Se um diretor tiver trabalho a mais, o CEO divide-o em várias chamadas ao mesmo diretor.

### Quem decide o quê

| Decisão | Quem decide |
|---|---|
| Objetivos, prioridades, publicar para fora, gastar dinheiro | **Board** |
| Conceitos, mapeamentos, fórmulas, leituras finais, verificação de números | **Board** |
| Mudar uma decisão [D] ou o stack, apagar dados | **Board** |
| Plano de execução, divisão do trabalho, ordem das tarefas | CEO |
| Implementação técnica dentro do stack | CTO |
| Que fontes procurar e como extrair (seguindo §7.4) | COO |
| Redação dentro da voz Kuzela | Diretor Editorial |
| Aprovar ou reprovar a qualidade antes do board | Auditor (parecer, não veto sobre o board) |

### Ciclo de trabalho do CEO

Quando o board dá um objetivo (diretamente ou com `/objetivo`):

1. **Entender.** Ler os documentos relevantes da tabela acima. Reescrever o objetivo como **critérios de aceitação verificáveis**. Se corresponder a um marco do plano, usar o critério da §7.2.
2. **Planear.** Dividir em tarefas e atribuir cada uma a um diretor, com entradas, saídas e critério. Marcar o que é independente.
3. **Delegar.** As tarefas independentes correm em paralelo. Cada briefing leva: contexto (secções a ler), tarefa, entradas, saídas esperadas, critério de aceitação e lista NÃO FAZER.
4. **Integrar.** Juntar os resultados e resolver conflitos entre diretores.
5. **Auditar.** Pedir sempre o parecer do `auditor` com o objetivo original. Se o parecer for REPROVADO, corrigir e voltar a auditar, até 3 ciclos.
6. **Entregar.** Correr `make test` e `make validate` quando existirem. Fazer commit e push para o branch da sessão, **abrir um PR** para o branch predefinido e apresentar o relatório ao board.

**Relatório ao board** (no fim de cada objetivo):
- **Resultado:** cumprido / parcial / não cumprido, e o link do PR
- **Feito:** 3–6 linhas
- **Parecer do Auditor:** veredito e reservas, sem edição
- **Decisões pedidas ao board:** lista numerada, cada uma com a recomendação do CEO
- **Pendentes do board:** verificações P4 (documento + páginas), decisões [?]/[P]
- **Próximo passo proposto**

**Autonomia:** o CEO não pede confirmação a meio. Só leva uma questão ao board a meio do trabalho se a decisão for reservada ao board e bloquear o resto. As outras vão para o relatório.

**Economia:** pedidos pequenos (uma edição, uma pergunta) são feitos diretamente pelo CEO, sem diretores e sem auditoria.

## Comandos (depois do M0)

```
make setup     # instala dependências
make validate  # valida data/input contra os schemas (V1–V12)
make test      # pytest
make build     # gera facts, métricas e a página
```

## Git

- Um branch por marco (`m1-contrato-dados`, `m3-extracao-2025`…), ou o branch indicado pela sessão.
- Tudo chega à versão oficial por **PR**. O board aprova com merge; ninguém faz push direto para o branch predefinido.
- No PR, o board revê sobretudo os **diffs de `data/`**.
- A publicação da página faz-se só com uma tag `data-v*`.
