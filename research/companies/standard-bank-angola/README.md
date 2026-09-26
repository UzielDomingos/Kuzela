# Standard Bank Angola — Caso 01

Pasta de trabalho do primeiro caso. Serve para perceber, à mão, como o trabalho realmente acontece antes de o transformar em pipeline, skills ou agentes.

```
01_documents  →  02_extraction  →  03_data  →  04_charts  →  05_output  →  Canva
```

| Pasta | O que entra | O que não entra |
|---|---|---|
| `01_documents/` | Documentos fonte oficiais, com o nome original. No git só fica o `manifest.csv` (URL, fonte, sha256). Os PDFs ficam em `01_documents/pdf/`, fora do git. | Excel transformados, screenshots, imprensa |
| `02_extraction/` | Mapa das demonstrações (documento → página) e valores tal como impressos, com página | Normalização, conceitos |
| `03_data/` | Dados transformados: original → normalizado → métrica → período → fonte → página | Números sem fonte |
| `04_charts/` | Fluxo financeiro a representar (primeiro em texto) e depois o Sankey com dados reais | Gráficos com valores ilustrativos não marcados |
| `05_output/` | Peça final pronta a levar para o Canva | — |

Os templates do Canva ficam intocados até ao `05_output`.

Cada decisão tomada pelo caminho fica em [`DECISOES.md`](DECISOES.md). São essas decisões que mais tarde se transformam em regras, skills e agentes.

## Estado

| Passo | Estado |
|---|---|
| 1. Criar a pasta | feito (26/09/2026) |
| 2. Encontrar e guardar os documentos primários | inventário feito; **download pendente** (ver `01_documents/README.md`) |
| 3. Identificar as demonstrações financeiras | pendente (precisa dos PDFs) |
| 4. Extrair os primeiros dados | pendente |
| 5. Primeiro Sankey com dados reais | pendente |
| 6. Voltar ao Canva | pendente |
