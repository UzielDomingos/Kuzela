# 01_documents — documentos fonte

Só documentos oficiais, com o nome original. Nada de Excel transformado, screenshots ou imprensa.

- `manifest.csv` — inventário (vai para o git): URL oficial, emissor, data de consulta, sha256, nº de páginas, estado.
- `pdf/` — os ficheiros em si (fora do git, regra 5). Guardar com o `nome_ficheiro_original`.

## Estados

| Estado | Significado |
|---|---|
| `por_encontrar` | Sabemos que existe (ou deve existir), ainda sem URL oficial |
| `url_encontrado` | URL oficial conhecido, ficheiro ainda não descarregado |
| `descarregado_drive` | Existe uma cópia no Google Drive do Uziel; falta copiar para `pdf/` e calcular o sha256 |
| `descarregado` | Ficheiro em `pdf/`, sha256 e nº de páginas preenchidos |

## Bloqueio atual

O ambiente cloud onde o Claude trabalha não consegue aceder a `www.standardbank.co.ao` nem a `standardinvest.co.ao`: a política de rede bloqueia-os. Os URLs vieram de uma pesquisa web e **ainda não foram abertos**. Até alguém os descarregar, `sha256` e `paginas` ficam vazios.

Duas saídas:
1. **Uziel descarrega** os documentos de prioridade 1 e 2 para `pdf/` (localmente e no Google Drive) e cola aqui os sha256 (`sha256sum pdf/*.pdf`).
2. **Liberar os domínios** nas definições de rede do ambiente (menu do ambiente → Edit → Network access: adicionar `www.standardbank.co.ao`, `www.cmc.ao`, `www.bodiva.ao`, `standardinvest.co.ao`). Assim o Claude descarrega e calcula o sha256 sozinho.

## Prioridades

1. R&C 2025 (PT) e prospecto da OPV — alimentam a peça CGD-01 e o primeiro Sankey.
2. R&C 2024, versão EN do R&C 2025, anúncio de lançamento, semestral de 2026.
3. e 4. Série histórica e controlos cruzados.

Ainda por procurar: apresentação a investidores (roadshow), documentos da CMC/IGAPE sobre a OPV, comunicados oficiais (resultado da OPV a 28/09 e início de negociação a 30/09).

## O que há na pasta Uziel OS (Google Drive), consultada a 26/09

- `SBA_Relatório de Disciplina de Mercado 2024.pdf`: documento oficial do SBA, registado no manifesto.
- `relatorio-e-contas-individual-e-consolidado-2025.pdf` **não é do SBA**: é o R&C 2025 do **Grupo BAI** (o índice diz "O Grupo BAI"). Fica para a peça BAI de 21/10.
- O R&C 2025 do SBA **não está no Drive**. O rascunho KR-001 cita as páginas 13–351, por isso o Uziel tem-no localmente. Pedido: carregá-lo para o Drive (ou para `pdf/`).
