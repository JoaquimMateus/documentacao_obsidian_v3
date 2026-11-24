# Índice — Testes ServiceNow (Mateus)

**Data:** 24/11/2026  
**Objetivo:** Isolar as consultas da API do ServiceNow (`config.yml`), validar os retornos reais em JSON e testar a compatibilidade com o ETL atual (`etl_transformacao_dados.py` e `queries.yml`) **sem alterar os arquivos base**.

---

## Status Geral das Tabelas Testadas

| Tabela | Consulta API (`etl_consulta_api.py`) | Transformação (`etl_transformacao_dados.py`) | Carga BD (`queries.yml`) |
| :--- | :--- | :--- | :--- |
| **Escalonamento** | ✅ 2.366 registros | ❌ Quebra (`KeyError` por mudança no `config.yml`) | ⚠️ Ativa, mas quebraria por prefixo no `ID` |
| **Pesquisa Pronto** | ✅ 1.111 registros | ❌ Quebra (`KeyError: CATEGORIA_METRICA` ausente) | ⚠️ Comentada no `queries.yml` |
| **Incidente** | ⚠️ 0 registros (filtro `u_rpt_paiSTARTSWITHCS`) | ❌ Quebra (`KeyError` por DataFrame vazio) | ⚠️ Comentada no `queries.yml` |

---

## Contextos Detalhados

* **Ambiente e Execução:**
  * [[Contexto - Ambiente e Scripts Isolados]]
* **Escalonamento:**
  * [[Escalonamento - Consulta e Mudanças no config.yml]]
  * [[Escalonamento - Impacto no ETL e Banco]]
* **Pesquisa Pronto:**
  * [[Pesquisa Pronto - Consulta e Mudanças no Retorno]]
  * [[Pesquisa Pronto - Impacto no ETL e Banco]]
* **Incidente:**
  * [[Incidente - Diagnóstico do Filtro e Impacto no ETL]]
