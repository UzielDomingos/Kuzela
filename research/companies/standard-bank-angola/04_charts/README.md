# 04_charts — Sankey Builder

Não se começa pelo gráfico bonito. Primeiro escreve-se em texto o fluxo que as demonstrações permitem mostrar, e só depois se desenha.

Candidatos (a escolher quando os documentos estiverem lidos):

- **Resultados:** produto bancário (margem financeira + comissões + outros) → custos operativos → imparidades → impostos → lucro líquido.
- **Balanço:** ativo (crédito, caixa e disponibilidades, investimentos) ↔ passivo (depósitos, outros) + capital próprio.

Cada fluxo tem de fechar (as entradas somam as saídas) com números do mesmo documento, período e escala.

## Regra que já existe: D5

O decision log da Kuzela (`KUZELA_RESEARCH_TEMPLATE_DECISIONS.md`, no Drive) fixa: **cascata por defeito**, que responde "como passámos de A para B?"; o **Sankey é a exceção**, que responde "para onde flui o dinheiro?". O rascunho KR-001 usou uma cascata (juros recebidos → juros pagos → margem financeira → outras receitas → produto bancário). O Sankey deste caso tem de justificar porque é exceção.

Ferramentas existentes (fora do repositório, na pasta local do Uziel): `tools/sankey_builder.html`, `tools/importador.js`, `tools/kuzela_research_charts.py`.
