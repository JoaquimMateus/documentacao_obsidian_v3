# Relatório Comparativo: Pesquisa Pronto e Incidente (Antes vs. Atual) & Impacto no ETL

**Data da Análise:** 24/11/2026  
**Diretório de Testes Isolados:** `/home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus`  
**Arquivos Base:** Intocados ([`config.yml`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/config.yml), [`etl_consulta_api.py`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/etl_consulta_api.py), [`etl_transformacao_dados.py`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/etl_transformacao_dados.py), [`queries.yml`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/queries.yml)).

---

## Parte 1 — `pesquisa_pronto` (`asmt_metric_result`)

### 1.1 Datas, Volumes e Arquivos Comparados

| Métrica / Parâmetro | Execução Anterior (Base Legada) | Nova Execução (Consulta Isolada) |
| :--- | :--- | :--- |
| **Arquivo JSON** | [`arquivos_intermediarios/processados/pesquisa_pronto_202606030343.json`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/arquivos_intermediarios/processados/pesquisa_pronto_202606030343.json) | [`TesteV3/Teste_Mateus/pesquisa_pronto.json`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus/pesquisa_pronto.json) |
| **Script de Coleta** | [`etl_consulta_api.py`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/etl_consulta_api.py) | [`TesteV3/Teste_Mateus/consulta_pesquisa_pronto.py`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus/consulta_pesquisa_pronto.py) |
| **Data da Extração** | **03/06/2026 às 03:43** | **24/11/2026 às 11:27** |
| **Janela da Query** | Últimos 30 dias (`instance.task_id.opened_atBETWEEN...`) | Últimos 30 dias (`2026-10-25@2026-11-24`) |
| **Faixa `opened_at` retornada** | `04/05/2026 07:39:34` até `02/06/2026 14:33:58` | `25/10/2026 21:31:14` até `16/11/2026 11:07:10` |
| **Volume de Registros** | **2.370 registros** | **1.111 registros** (pág. 1: 964 + pág. 2: 147) |
| **Tamanho do Arquivo** | ~1.9 MB | 0.45 MB |

---

### 1.2 Matriz DE-PARA de Colunas e Formato (`pesquisa_pronto`)

Todos os campos solicitados no `config.yml` permanecem iguais entre as versões (`sysparm_display_value: "true"` com dot-walking).

| Campo Solicitado (`config.yml`) | Chave no JSON Anterior (03/06/2026) | Chave no JSON Novo (24/11/2026) | Tipo | Presença nos Registros (Antes → Agora) | Formato Igual? | Coluna Final (`DEPARA_COLUNAS`) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `instance.number` | `instance.number` | `instance.number` | `str` | 2.370/2.370 → 1.111/1.111 | **Sim** | `ID_RESPOSTA` |
| `instance.task_id.number` | `instance.task_id.number` | `instance.task_id.number` | `str` | 2.352/2.370 → 484/1.111 | **Sim** | `ID_TICKET` |
| `instance.metric_type.name` | `instance.metric_type.name` | ❌ **AUSENTE** | `str` | **1.060/2.370 → 0/1.111** | ⚠️ **Chave ausente** | `CATEGORIA_METRICA` |
| `string_value` | `string_value` | `string_value` | `str` | 2.370/2.370 → 1.111/1.111 | **Sim** | `VALOR_METRICA` |
| `actual_value` | `actual_value` | `actual_value` | `str` | 2.370/2.370 → 1.111/1.111 | **Sim** | `VALOR_NUM` |
| `instance.task_id.opened_at` | `instance.task_id.opened_at` | `instance.task_id.opened_at` | `str` | 2.352/2.370 → 484/1.111 | **Sim** | `ABERTURA_TICKET_HR` |
| `instance.task_id.closed_at` | `instance.task_id.closed_at` | `instance.task_id.closed_at` | `str` | 2.352/2.370 → 484/1.111 | **Sim** | `FECHAMENTO_TICKET_HR` |
| `instance.task_id.opened_by.name` | `instance.task_id.opened_by.name` | `instance.task_id.opened_by.name` | `str` | 2.352/2.370 → 484/1.111 | **Sim** | `SOLICITANTE` |
| `instance.task_id.assignment_group.name` | `instance.task_id.assignment_group.name` | `instance.task_id.assignment_group.name` | `str` | 2.352/2.370 → 484/1.111 | **Sim** | `GRUPO` |
| `instance.task_id.state` | `instance.task_id.state` | `instance.task_id.state` | `str` | 2.352/2.370 → 484/1.111 | **Sim** | `STATUS_TICKET` |
| `instance.task_id.description` | `instance.task_id.description` | `instance.task_id.description` | `str` | 2.352/2.370 → 484/1.111 | **Sim** | `DESCRICAO_TICKET` |
| `instance.task_id.ref_sn_customerservice_case.account.name` | `instance.task_id.ref_sn_customerservice_case.account.name` | `instance.task_id.ref_sn_customerservice_case.account.name` | `str` | 2.352/2.370 → 484/1.111 | **Sim** *(não mapeado no DEPARA)* | *(comentado no config.yml)* |

### 1.3 Diferenças Estruturais / de Conteúdo Observadas

1. **Formato dos valores:** Os campos continuam retornando como strings planas (`str`), sem objetos `{display_value, link}`.
2. **Desaparecimento de `instance.metric_type.name`:**
   - Na coleta de **03/06/2026**, o campo `instance.metric_type.name` vinha preenchido em 1.060 dos 2.370 registros (44,7%).
   - Na coleta de **24/11/2026**, o campo **não veio em nenhum dos 1.111 registros (0%)**.
3. **Queda drástica nos campos `instance.task_id.*`:**
   - Na coleta de **03/06/2026**, apenas 18 registros (0,8%) vinham sem `instance.task_id.*` (`CS`: 1.251, `PT`: 651, `RI`: 320, `PL`: 126, `IN`: 4).
   - Na coleta de **24/11/2026**, **627 dos 1.111 registros (56,4%)** retornaram apenas com 3 campos (`instance.number`, `string_value`, `actual_value`), sem nenhum dado do ticket (`CS`: 210, `RI`: 224, `PT`: 33, `IN`: 8, `PL`: 9).

### 1.4 Amostras Lado a Lado (`pesquisa_pronto`)

* **Antes (03/06/2026 — registro com `instance.metric_type.name`):**
  ```json
  {
    "instance.task_id.ref_sn_customerservice_case.account.name": "",
    "instance.task_id.number": "RITM0133219",
    "instance.task_id.closed_at": "02/06/2026 02:01:15",
    "instance.number": "AINST1176748",
    "string_value": "Ótimo",
    "instance.task_id.assignment_group.name": "Job Executor - DIOT",
    "instance.task_id.opened_by.name": "Gustavo Furtado Pereira",
    "instance.task_id.state": "Encerrado",
    "actual_value": "1",
    "instance.metric_type.name": "Pesquisa de Satisfação - Solicitações DATAPREV",
    "instance.task_id.opened_at": "25/05/2026 10:31:56",
    "instance.task_id.description": "..."
  }
  ```

* **Agora (24/11/2026 — registro completo vs. registro sem `task_id`):**
  ```json
  [
    {
      "instance.task_id.ref_sn_customerservice_case.account.name": "MTP",
      "instance.task_id.number": "CS0721255",
      "instance.task_id.closed_at": "27/10/2026 08:18:38",
      "instance.number": "AINST1372755",
      "string_value": "Muito satisfeito",
      "instance.task_id.assignment_group.name": "N1.Clientes-Externos",
      "instance.task_id.opened_by.name": "Walter Aparecido Pedrosa",
      "instance.task_id.state": "Encerrado",
      "actual_value": "5",
      "instance.task_id.opened_at": "27/10/2026 06:06:35",
      "instance.task_id.description": "..."
    },
    {
      "instance.number": "AINST1393867",
      "string_value": "Tempo de atendimento",
      "actual_value": "3"
    }
  ]
  ```

### 1.5 Análise do ETL: O Código Funcionaria para `pesquisa_pronto`?

* **Consulta (`etl_consulta_api.py`):** ✅ **Funciona** (HTTP 200, paginação normal, 1.111 registros).
* **Transformação (`etl_transformacao_dados.py`):** ❌ **NÃO FUNCIONA HOJE!**
  - **Na base antiga (03/06/2026):** a simulação concluiu com sucesso (`Shape final: (2370, 11)`), batendo com o registrado em [`logs/TransformaDados.log`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/logs/TransformaDados.log#L57-L62).
  - **Na base atual (24/11/2026):** como a chave `instance.metric_type.name` não aparece em nenhum dos 1.111 registros, o `pd.json_normalize` cria apenas 11 colunas (sem `instance.metric_type.name`). No `renomear_colunas()`, a coluna `CATEGORIA_METRICA` não é criada. Em seguida, o `ordenar_colunas()` falha com:
    ```text
    KeyError: "['CATEGORIA_METRICA'] not in index"
    ```
    e encerra com `sys.exit(1)`, impedindo a geração do CSV.
* **Carregamento (`etl_carregamento_dados.py` / `queries.yml`):** ⚠️ **Desabilitado.**
  - O bloco `PRONTO_PESQUISA` em [`queries.yml`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/queries.yml#L42-L51) está totalmente comentado (`#`), portanto o `etl_carregamento_dados.py` **não insere** esses dados na tabela `PRONTO_SURVEY`.

---

## Parte 2 — `incidente` (`incident`)

### 2.1 Datas, Volumes e Arquivos Comparados

| Métrica / Parâmetro | Execução Anterior (Base Legada) | Nova Execução (Consulta Isolada) | Diagnóstico Sem Filtro `u_rpt_pai` |
| :--- | :--- | :--- | :--- |
| **Arquivo JSON** | [`arquivos_intermediarios/incidente.json`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/arquivos_intermediarios/incidente.json) e [`incidente_2026_06_02 03_06.json`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/arquivos_intermediarios/processados/incidente_2026_06_02%2003_06.json) | [`TesteV3/Teste_Mateus/incidente.json`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus/incidente.json) | Teste diagnóstico (`--sem-filtro-incidente --limite-maximo 5`) |
| **Script de Coleta** | [`etl_consulta_api.py`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/etl_consulta_api.py) | [`TesteV3/Teste_Mateus/consulta_incidente.py`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus/consulta_incidente.py) | [`consulta_incidente.py`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus/consulta_incidente.py) |
| **Data da Extração** | **02/06/2026 e 03/06/2026** (e desde dez/2025) | **24/11/2026 às 11:27** | **24/11/2026 às 11:27** |
| **Query Aplicada** | `opened_atBETWEEN...^u_rpt_paiSTARTSWITHCS^OR...` | `opened_atBETWEEN2026-10-25@2026-11-24^u_rpt_paiSTARTSWITHCS^ORsys_updated_onBETWEEN2026-10-25@2026-11-24^u_rpt_paiSTARTSWITHCS` | `opened_atBETWEEN2026-10-25@2026-11-24^ORsys_updated_onBETWEEN2026-10-25@2026-11-24` |
| **Volume de Registros** | **0 registros** (`{"result": []}`) | **0 registros** (`"result": []`) | **1.000+ registros na pág. 1** (limitado a 5 no teste) |

> **Descoberta importante:** Mesmo removendo o filtro de datas (`--sem-filtro-data`), a consulta com `u_rpt_paiSTARTSWITHCS` retorna **0 registros**. Porém, ao remover `u_rpt_paiSTARTSWITHCS` (`--sem-filtro-incidente`), a API retorna milhares de incidentes normalmente — e neles o campo `u_rpt_pai` vem vazio (`""`). Isso indica que todos os arquivos históricos de `incidente` desde pelo menos dezembro/2025 vieram vazios por causa desse filtro.

---

### 2.2 Matriz DE-PARA de Colunas (`incidente`)

Como o retorno oficial (tanto o anterior de 02–03/06/2026 quanto o novo de 24/11/2026) possui **0 registros**, o mapeamento abaixo compara os campos configurados no `config.yml` com o retorno obtido no **teste diagnóstico sem o filtro `u_rpt_paiSTARTSWITHCS`**:

| Campo Solicitado (`config.yml`) | JSON Oficial (Antes & Agora) | JSON Diagnóstico (sem `u_rpt_paiSTARTSWITHCS`) | Tipo | Formato | Coluna Final (`DEPARA_COLUNAS`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `number` | *(vazio)* | `number` (`'INC0182979'`) | `str` | Plano | `ID_INC` |
| `origin_table` | *(vazio)* | `origin_table` (`''`) | `str` | Plano | `TABELA_FONTE` |
| `category` | *(vazio)* | `category` (`'Infraestrutura'`) | `str` | Plano | `CATEGORIA` |
| `subcategory` | *(vazio)* | `subcategory` (`'Backup'`) | `str` | Plano | `SUBCATEGORIA` |
| `impact` | *(vazio)* | `impact` (`'3 - Baixo(a)'`) | `str` | Plano | `IMPACTO` |
| `priority` | *(vazio)* | `priority` (`'4 - Baixa'`) | `str` | Plano | `PRIORIDADE` |
| `parent.number` | *(vazio)* | ⚠️ *(ausente nos 5 registros da amostra)* | `str` | Plano | `ID_TICKET_PAI` |
| `state` | *(vazio)* | `state` (`'Encerrado'`) | `str` | Plano | `STATUS` |
| `opened_at` | *(vazio)* | `opened_at` (`'11/11/2026 01:32:22'`) | `str` | Plano | `ABERTO_EM` |
| `opened_by.name` | *(vazio)* | `opened_by.name` (`'Clarino Freitas de Sousa'`) | `str` | Plano | `ABERTO_POR` |
| `sys_updated_on` | *(vazio)* | `sys_updated_on` (`'18/11/2026 02:00:00'`) | `str` | Plano | `ATUALIZADO_EM` |
| `assignment_group.name` | *(vazio)* | `assignment_group.name` (`'Backup - DIDC'`) | `str` | Plano | `GRUPO_ATRIBUICAO` |
| `assigned_to.name` | *(vazio)* | `assigned_to.name` (`'Fernando Soffa Chaves'`) | `str` | Plano | `ATRIBUIDO_A` |
| `resolved_by.name` | *(vazio)* | `resolved_by.name` (`'Fernando Soffa Chaves'`) | `str` | Plano | `RESOLVIDO_POR` |
| `business_service.name` | *(vazio)* | `business_service.name` (`'Proteção de Dados-DF-PRD'`) | `str` | Plano | `SERVICO` |
| `resolved_at` | *(vazio)* | `resolved_at` (`'11/11/2026 01:47:35'`) | `str` | Plano | `RESOLVIDO_EM` |
| `closed_at` | *(vazio)* | `closed_at` (`'18/11/2026 02:00:00'`) | `str` | Plano | `FECHADO_EM` |
| `active` | *(vazio)* | `active` (`'Falso(a)'`) | `str` | Plano | `ATIVO` |
| `action_status` | *(vazio)* | `action_status` (`''`) | `str` | Plano | `STATUS_ACOES` |
| `u_symptom` | *(vazio)* | `u_symptom` (`'Mensagem de Erro'`) | `str` | Plano | `SINTOMA` |
| `description` | *(vazio)* | `description` | `str` | Plano | `DESCRICAO` |
| `short_description` | *(vazio)* | `short_description` | `str` | Plano | `DESCRICAO_RESUMIDA` |
| `u_rpt_pai` | *(vazio)* | `u_rpt_pai` (`''`) | `str` | Plano | *(não mapeado no DEPARA)* |

### 2.3 Análise do ETL: O Código Funcionaria para `incidente`?

* **Consulta (`etl_consulta_api.py`):** ⚠️ **Retorna HTTP 200, mas sempre com 0 registros (`{"result": []}`)** devido ao filtro `sysparm_query_incidente: "u_rpt_paiSTARTSWITHCS"`.
* **Transformação (`etl_transformacao_dados.py`):** ❌ **NÃO FUNCIONA!**
  - Como `dados_brutos` é `[]`, `pd.json_normalize([])` gera um DataFrame vazio `(0, 0)` sem nenhuma coluna.
  - Em `ordenar_colunas()`, o Pandas lança `KeyError` (`None of [Index(['ID_INC', ...])] are in the [columns]`), registra `ERROR incidente - Falha na ordenação de colunas.` e encerra o processo com `sys.exit(1)` — exatamente como já vinha acontecendo no [`logs/TransformaDados.log`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/logs/TransformaDados.log#L24-L28) em 02/06/2026 e 03/06/2026.
  - Nota adicional: mesmo se a consulta retornasse registros (como no teste sem `u_rpt_paiSTARTSWITHCS`), caso nenhum incidente do lote possua `parent.number` preenchido, a coluna `ID_TICKET_PAI` também faltará e provocará o mesmo `KeyError` no `ordenar_colunas()`.
* **Carregamento (`etl_carregamento_dados.py` / `queries.yml`):** ⚠️ **Desabilitado.**
  - Tanto o bloco `INCIDENTE` ([`queries.yml`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/queries.yml#L53-L62)) quanto o `SnapshotINC` ([`queries.yml`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/queries.yml#L78-L80)) estão comentados (`#`).

---

## Parte 3 — Resumo Comparativo Final

| Aspecto | `pesquisa_pronto` | `incidente` |
| :--- | :--- | :--- |
| **API retorna dados hoje?** | **Sim** (1.111 registros nos últimos 30 dias) | **Não com a query atual** (0 registros com `u_rpt_paiSTARTSWITHCS`; 1.000+ sem esse filtro) |
| **Formato de retorno mudou?** | **Não** (continua string plana), mas `instance.metric_type.name` sumiu e `task_id.*` caiu para 43,6% | **Inalterado** (0 registros antes e agora) |
| **`etl_transformacao_dados.py` funciona hoje?** | ❌ **Quebra** (`KeyError: ['CATEGORIA_METRICA'] not in index`) | ❌ **Quebra** (`KeyError` porque `result` é `[]` e DF não tem colunas) |
| **`etl_carregamento_dados.py` carrega no BD?** | **Não** (bloco `PRONTO_PESQUISA` comentado em `queries.yml`) | **Não** (bloco `INCIDENTE` comentado em `queries.yml`) |

