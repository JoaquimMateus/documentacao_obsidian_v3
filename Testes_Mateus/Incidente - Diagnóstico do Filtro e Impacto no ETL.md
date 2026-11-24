# Incidente — Diagnóstico do Filtro e Impacto no ETL

**Relacionado:** [[Índice - Testes ServiceNow (Mateus)]]

---

## Datas e Volumes Comparados

| Métrica | Base Anterior | Nova Coleta Isolada | Diagnóstico Sem Filtro |
| :--- | :--- | :--- | :--- |
| **Arquivo** | `incidente_2026_06_02 03_06.json` | `TesteV3/Teste_Mateus/incidente.json` | Teste com `--sem-filtro-incidente` |
| **Data** | 02/06/2026 e 03/06/2026 | 24/11/2026 às 11:27 | 24/11/2026 às 11:27 |
| **Registros** | **0** (`{"result": []}`) | **0** (`"result": []`) | **1.000+ registros** na 1ª página |

---

## Causa do Retorno Vazio
* O `etl_consulta_api.py` aplica o filtro `u_rpt_paiSTARTSWITHCS` (`SERVICENOW.sysparm_query_incidente` no `config.yml`).
* Mesmo sem filtro de data (`--sem-filtro-data`), `u_rpt_paiSTARTSWITHCS` retorna **0 registros**.
* Sem esse filtro, a API retorna milhares de incidentes normalmente (com `u_rpt_pai` vazio `""`).

---

## O Código do ETL Funcionaria Hoje?

* **Consulta (`etl_consulta_api.py`):** ⚠️ Retorna HTTP 200, mas sempre vazio (`[]`).
* **Transformação (`etl_transformacao_dados.py`):** ❌ **NÃO FUNCIONA.**
  * `pd.json_normalize([])` gera um DataFrame vazio `(0, 0)` sem colunas.
  * `ordenar_colunas()` falha com `KeyError` e encerra com `sys.exit(1)` (comprovado em `logs/TransformaDados.log` em 02/06 e 03/06).
* **Carga no Banco (`queries.yml`):** ⚠️ **Desabilitada.** Os blocos `INCIDENTE` e `SnapshotINC` estão comentados (`#`) em `queries.yml`.
