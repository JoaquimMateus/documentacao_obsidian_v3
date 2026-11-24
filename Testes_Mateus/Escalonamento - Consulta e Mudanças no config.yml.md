# Escalonamento — Consulta e Mudanças no `config.yml`

**Relacionado:** [[Escalonamento - Impacto no ETL e Banco]] | [[Índice - Testes ServiceNow (Mateus)]]

---

## Datas e Volumes Comparados

| Métrica | Base Anterior | Nova Coleta Isolada |
| :--- | :--- | :--- |
| **Arquivo** | `escalonamento_202603180333.json` | `TesteV3/Teste_Mateus/escalonamento.json` |
| **Data** | 18/03/2026 às 03:33 | 24/11/2026 às 11:00 |
| **Período** | Histórico até mar/2026 | Últimos 30 dias (25/10/2026 a 24/11/2026) |
| **Registros** | 7.694 | 2.366 |

---

## O Que Mudou no `config.yml` (`sysparm_fields`)
* **Antes (dot-walking):** `escalation_severity.name`, `source_record.number`, `assignment_group.name`, `assigned_to.name`, `de_escalated_by.name`
* **Agora (campo direto):** `escalation_severity`, `source_record`, `assignment_group`, `assigned_to`, `de_escalated_by`

---

## DE-PARA de Formato no JSON

* **Campos primitivos (`str`):** `number`, `state`, `source_table`, `opened_at`, `sys_updated_on`, `closed_at` — **iguais**.
* **Campos de referência:** mudaram de **string simples** para **dicionário** `{"display_value": "...", "link": "..."}` (ou `""` quando vazio):
  * `escalation_severity.name` (`"Gravidade alta"`) → `escalation_severity` (`{"display_value": "Gravidade alta", "link": "..."}`)
  * `assignment_group.name` (`"GP061 (Atendimento)"`) → `assignment_group` (`{"display_value": "GP055 (Atendimento)", "link": "..."}`)
  * `source_record.number` (`"CS0211993"`) → `source_record` (`{"display_value": "Solicitação: CS0721537", "link": "..."}`) ⚠️ *inclui o prefixo `"Solicitação: "`*
  * `assigned_to.name` → `assigned_to` (`{"display_value": "...", "link": "..."}` ou `""`)
  * `de_escalated_by.name` → `de_escalated_by` (`{"display_value": "...", "link": "..."}` ou `""`)
