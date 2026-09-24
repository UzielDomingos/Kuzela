---
description: Dá uma tarefa + objetivos e a equipa de agentes executa do princípio ao fim
argument-hint: <tarefa e objetivos>
---
Tarefa e objetivos do Uziel:

$ARGUMENTS

Executa isto seguindo o **"Modo de trabalho: orquestração"** do `CLAUDE.md`, do início ao fim:
1. Lê o contexto relevante e reescreve os objetivos como critérios de aceitação verificáveis (mostra-os numa lista curta).
2. Planeia e delega aos subagentes (`pesquisador`, `extrator`, `construtor`, `editor`), em paralelo quando as subtarefas são independentes.
3. Integra e chama sempre o `revisor` no fim; corrige e repete até aprovado (máx. 3 ciclos).
4. Commit + push para o branch da sessão.
5. Resumo final: feito · falta · decisões tomadas · o que precisa do Uziel.

Não pares para confirmar passos intermédios. Só perguntas se a decisão for genuinamente do Uziel (significado financeiro, dinheiro, apagar dados, publicar para fora, mudar uma decisão [D]).
