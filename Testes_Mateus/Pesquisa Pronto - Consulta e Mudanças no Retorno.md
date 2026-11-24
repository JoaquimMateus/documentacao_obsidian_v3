# Pesquisa Pronto — Consulta e Mudanças no Retorno

**Relacionado:** [[Pesquisa Pronto - Impacto no ETL e Banco]] | [[Índice - Testes ServiceNow (Mateus)]]

---

## Datas e Volumes Comparados

| Métrica | Base Anterior | Nova Coleta Isolada |
| :--- | :--- | :--- |
| **Arquivo** | `pesquisa_pronto_202606030343.json` | `TesteV3/Teste_Mateus/pesquisa_pronto.json` |
| **Data** | 03/06/2026 às 03:43 | 24/11/2026 às 11:27 |
| **Faixa `opened_at`** | 04/05/2026 a 02/06/2026 | 25/10/2026 a 16/11/2026 |
| **Registros** | 2.370 | 1.111 (pág. 1: 964 + pág. 2: 147) |

---

## O Que Mudou no Retorno da API (`asmt_metric_result`)

* **Formato:** Continua igual (strings planas, sem `{display_value, link}`).
* **Diferenças críticas nos dados:**
  1. **`instance.metric_type.name` desapareceu:** estava presente em **1.060 / 2.370** registros em 03/06/2026, e veio em **0 / 1.111** registros em 24/11/2026.
  2. **Queda nos campos `instance.task_id.*`:** caiu de **99,2%** (2.352/2.370) para **43,6%** (484/1.111). Os outros 627 registros retornam apenas com `instance.number`, `string_value` e `actual_value`.
