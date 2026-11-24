# Pesquisa Pronto — Impacto no ETL e Banco

**Relacionado:** [[Pesquisa Pronto - Consulta e Mudanças no Retorno]] | [[Índice - Testes ServiceNow (Mateus)]]

---

## O Código do ETL Funcionaria Hoje?

* **Consulta (`etl_consulta_api.py`):** ✅ **Funciona** (HTTP 200, 1.111 registros).
* **Transformação (`etl_transformacao_dados.py`):** ❌ **NÃO FUNCIONA HOJE.**
  * Na base antiga (03/06/2026), a transformação passava (`Shape: (2370, 11)`).
  * Na base atual (24/11/2026), como `instance.metric_type.name` não aparece em nenhum registro, a coluna `CATEGORIA_METRICA` não é criada no DataFrame.
  * Em `ordenar_colunas()`, o script falha com:
    ```text
    KeyError: "['CATEGORIA_METRICA'] not in index"
    ```
    e encerra com `sys.exit(1)`.
* **Carga no Banco (`queries.yml`):** ⚠️ **Desabilitada.** O bloco `PRONTO_PESQUISA` está comentado (`#`) em `queries.yml` (linhas 42–51).
