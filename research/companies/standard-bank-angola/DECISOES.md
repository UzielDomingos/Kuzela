# Registo de decisões — Caso 01 (Standard Bank Angola)

Cada vez que o trabalho obriga a escolher, fica aqui: o que se decidiu, porquê e quem tem de confirmar. São estas decisões que mais tarde se transformam em regras, skills e agentes.

Legenda: **[C]** decisão do Claude (operacional, reversível) · **[B?]** precisa do board · **[B]** decidida pelo board

| # | Data | Passo | Decisão | Porquê | Quem |
|---|---|---|---|---|---|
| C-01 | 26/09 | Estrutura | A pasta do caso vive em `research/companies/standard-bank-angola/` e não substitui a `sources/` + `data/input/` do plano (§5). Quando o M2 existir, o `manifest.csv` daqui migra para `sources/manifest.csv`. | O caso é exploratório; o plano continua a ser o destino. Evita duas fontes de verdade a prazo. | [B?] confirmar |
| C-02 | 26/09 | Documentos | PDFs em `01_documents/pdf/`, fora do git (`.gitignore` com `*.pdf` na raiz). No git só o `manifest.csv`. | Regra 5 do CLAUDE.md. | [C] |
| C-03 | 26/09 | Documentos | Colunas do manifesto: `doc_id, prioridade, tipo, periodo, lingua, titulo_no_resultado, nome_ficheiro_original, url, emissor, encontrado_via, data_consulta, sha256, paginas, estado, notas`. | É o mínimo para voltar à fonte e provar que o ficheiro não mudou. Semente do futuro `documents.csv`. | [C] |
| C-04 | 26/09 | Documentos | O documento primário é a **versão portuguesa** do R&C; a inglesa serve só para controlo cruzado. | Os números oficiais estão publicados em PT; a tradução pode ter erros. | [B?] confirmar |
| C-05 | 26/09 | Documentos | Os URLs encontrados por pesquisa web ficam como `url_encontrado`, e não como `descarregado`, até o ficheiro ser aberto e o sha256 calculado. | O site do banco está bloqueado neste ambiente: não se afirma o que não se abriu. | [C] |
| C-06 | 26/09 | Documentos | O "Prospecto de base assinado" **não** é tratado como prospecto da OPV. | [I] O nome sugere um programa de emissão de dívida. Confirmar ao abrir. | [C] |
| C-07 | 26/09 | Documentos | Os factos da OPV vindos da imprensa (datas, preço 41 220–50 000 Kz, 34 %, 4,76 M de ações) ficam marcados [NV] até serem lidos no prospecto. | A imprensa só serve de contexto (CLAUDE.md, regra 1). | [C] |

## Perguntas em aberto para o board

1. **R&C 2025 "light":** o ficheiro PT chama-se `SBA_RelatorioContas_31_2_2025_light.pdf`. Se for uma versão resumida sem as notas, onde está a versão completa?
2. **Perímetro:** o semestral de 2025 diz "Consolidado". O Sankey e a peça usam as contas individuais ou as consolidadas? (Decisão de significado financeiro → board.)

## Adições depois de consultar a pasta Uziel OS (26/09)

| # | Data | Passo | Decisão | Porquê | Quem |
|---|---|---|---|---|---|
| C-08 | 26/09 | Documentos | Novo estado `descarregado_drive` no manifesto: o ficheiro existe no Drive do Uziel mas ainda não tem sha256. | O Drive é a cópia de arquivo prevista no plano (§1, Storage), mas não prova a integridade. | [C] |
| C-09 | 26/09 | Extração | Os números do rascunho KR-001 entram só como `a_reverificar`, e não como dados extraídos. | Foram escritos antes deste processo e sem registo célula a célula. Servem como mapa de páginas e como controlo. | [C] |
| C-10 | 26/09 | Charts | O caso segue a D5 (cascata por defeito, Sankey como exceção). O Sankey do passo 5 é um teste, não o formato por defeito da peça. | A D5 já está decidida no decision log; não se reabre. | [B] (D5) |
| C-11 | 26/09 | Documentos | O R&C 2025 do Drive é do BAI e não se mistura com o caso SBA. | O índice do PDF diz "O Grupo BAI". Nome de ficheiro genérico = risco de troca de documentos → **futura regra: identificar o emissor pela capa/índice, nunca pelo nome do ficheiro.** | [C] |

3. **Perímetro (reforço da pergunta 2):** o rascunho KR-001 já usa as contas **consolidadas**. Confirmas que o caso segue consolidado?
