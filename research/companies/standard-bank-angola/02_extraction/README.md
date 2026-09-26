# 02_extraction — o que está em cada documento

Ordem de trabalho:

```
PDF → identificar as demonstrações → extrair valores → registar a página → guardar o dado original
```

Primeiro ficheiro a criar: `mapa_demonstracoes.csv` — para cada documento, em que páginas estão o balanço, a demonstração de resultados, a demonstração de fluxos de caixa e as notas principais, e com que escala (milhares/milhões de Kz), perímetro (individual/consolidado) e colunas (anos, reexpresso?).

Depois: `valores_originais_<doc_id>.csv`, com os valores tal como impressos (`label_original`, `column_original`, `value_as_printed`, `pdf_page`, `printed_page`, `scale`, `status=extraido`). Ainda não se normaliza.

## Ponto de partida: o rascunho KR-001

A pasta Uziel OS (Google Drive, `KR-001_standard-bank/`) já tem um rascunho CGD-01 de 21/09, que cita o "Relatório Anual 2025, contas consolidadas". `kr001_rascunho_a_reverificar.csv` lista cada número desse rascunho e as páginas citadas. **Não são dados:** são afirmações a confirmar célula a célula quando o PDF estiver aberto. Dão também a primeira pista do mapa de demonstrações:

| Página citada | Hipótese [I] |
|---|---|
| 13 | Principais indicadores |
| 98–99 | Análise financeira (relatório de gestão) |
| 242 | Balanço consolidado |
| 243 | Demonstração de resultados consolidada |
| 351 | Nota de segmentos |
