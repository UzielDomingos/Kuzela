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

## Modo de trabalho: orquestração

Quando o Uziel der uma tarefa e objetivos (diretamente ou com `/objetivo`), a sessão principal é o **orquestrador** e faz isto:

1. **Entender.** Ler os documentos relevantes da tabela acima. Reescrever os objetivos como **critérios de aceitação verificáveis**. Se o pedido corresponder a um marco do plano, usar o critério da §7.2.
2. **Planear.** Dividir em subtarefas pequenas. Para cada uma, indicar o agente, as entradas, as saídas e o critério. Marcar as subtarefas que são independentes.
3. **Delegar.** Usar os subagentes de `.claude/agents/`. As subtarefas independentes correm em paralelo. Cada briefing leva: contexto (secções a ler), tarefa, entradas, saídas esperadas, critério de aceitação e lista NÃO FAZER.
4. **Integrar.** Juntar os resultados e resolver conflitos entre eles.
5. **Verificar.** Chamar sempre o `revisor` no fim, com os objetivos originais. Se houver falhas, corrigir e voltar a rever, até 3 ciclos.
6. **Entregar.** Correr `make test` e `make validate` quando existirem. Depois commit com uma mensagem clara, push para o branch da sessão e um resumo final: o que ficou feito, o que falta, as decisões tomadas e **o que precisa do Uziel** (verificações P4, decisões [?]/[P]).

**Autonomia:** não se pede confirmação a meio da tarefa. Só se pára para perguntar quando a decisão é realmente do Uziel: significado financeiro, gastar dinheiro, apagar dados, publicar para fora ou mudar uma decisão [D]. As perguntas que não bloqueiam vão para o resumo final.

**Economia:** tarefas pequenas (uma edição, uma pergunta) fazem-se diretamente, sem subagentes. Os subagentes são para trabalho com várias partes.

### Equipa de subagentes

| Agente | Faz | Não faz |
|---|---|---|
| `pesquisador` | Encontra fontes oficiais, dados de mercado, concorrência e factos com citação | Escrever ficheiros do projeto |
| `extrator` | Lê páginas de PDF e propõe `raw_facts` (prompt padrão §7.4) e mapeamentos | Marcar verificado ou decidir conceitos |
| `construtor` | Implementa os marcos M0–M9: schemas, scripts, testes, CI, página | Decidir significado financeiro ou sair do stack |
| `editor` | Rascunha e critica textos: leituras, peças CGD/MK/AIN, legendas, "O que isto não diz" | Opinar, recomendar ou inventar números |
| `revisor` | Verifica o trabalho contra os objetivos, as regras do projeto e o Guia | Corrigir o trabalho que está a rever |

## Comandos (depois do M0)

```
make setup     # instala dependências
make validate  # valida data/input contra os schemas (V1–V12)
make test      # pytest
make build     # gera facts, métricas e a página
```

## Git

- Um branch por marco (`m1-contrato-dados`, `m3-extracao-2025`…), ou o branch indicado pela sessão.
- O Uziel revê sobretudo os **diffs de `data/`**.
- A publicação da página faz-se só com uma tag `data-v*`.
