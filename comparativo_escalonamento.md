# Relatório Comparativo: Escalonamento (Antes vs. Novo) & Impacto no ETL

**Data da Análise:** 24/11/2026  
**Ambiente / Diretório de Teste:** `/home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus`  
**Arquivos Comparados:**
- **Base Histórica / Anterior:** [`arquivos_intermediarios/processados/escalonamento_202603180333.json`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/arquivos_intermediarios/processados/escalonamento_202603180333.json) (gerado em 18/03/2026 às 03:33:43)
- **Base Nova (Consulta Isolada):** [`TesteV3/Teste_Mateus/escalonamento.json`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus/escalonamento.json) (gerado em 24/11/2026 às 11:00:00)

---

## 1. Resumo Executivo das Datas e Volumes

| Métrica / Parâmetro | Execução Anterior (Base Legada) | Nova Execução (Consulta Isolada) |
| :--- | :--- | :--- |
| **Data da Coleta** | **18/03/2026 03:33:43** | **24/11/2026 11:00:00** |
| **Janela Temporal Filtrada** | Histórico acumulado até 18/03/2026 | Últimos 30 dias (25/10/2026 a 24/11/2026) |
| **Volume de Registros** | **7.694 registros** | **2.366 registros** |
| **Tamanho do Arquivo JSON** | 3.28 MB | 2.74 MB |
| **Endpoint Consultado** | `sn_customerservice_escalation` | `sn_customerservice_escalation` |
| **`sysparm_display_value`** | `true` | `true` |

---

## 2. O Que Mudou nos Parâmetros do `config.yml`

No arquivo [`config.yml`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/config.yml#L44), na chave `SERVICENOW -> parametros -> sysparm_fields -> escalonamento`:

*   **Configuração Anterior:**
    ```text
    number,state,escalation_severity.name,source_table,source_record.number,opened_at,assignment_group.name,assigned_to.name,sys_updated_on,de_escalated_by.name,closed_at
    ```
    *(Utilizava a notação dot-walking `.name` e `.number` para navegar e extrair strings diretas dos registros referenciados)*.

*   **Configuração Atualizada (Nova):**
    ```text
    number,state,escalation_severity,source_table,source_record,opened_at,assignment_group,assigned_to,sys_updated_on,de_escalated_by,closed_at
    ```
    *(Removeu os sufixos `.name` e `.number`, solicitando o campo de referência bruto)*.

---

## 3. Matriz DE-PARA: Nomes de Colunas, Formatos e Conteúdo

A tabela abaixo compara detalhadamente cada campo retornado no JSON antes e agora, como o Pandas (`pd.json_normalize`) os processa e como a transformação os mapeia.

| Campo Solicitado (Antes) | Campo Solicitado (Agora) | Chave no JSON Anterior | Chave no JSON Novo | Tipo Antigo | Tipo Novo | Informação Retornada é a Mesma? | Formato Está Igual? | Coluna Final Esperada (BD) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `number` | `number` | `number` | `number` | `str` | `str` | **Sim** (`'ESC...'`) | **Sim** (String direta) | `ID_ESC` |
| `state` | `state` | `state` | `state` | `str` | `str` | **Sim** (`'Encerrado'`) | **Sim** (String direta) | `STATUS` |
| `escalation_severity.name` | `escalation_severity` | `escalation_severity.name` | `escalation_severity` | `str` | `dict` | **Sim** (ex: `'Gravidade alta'`) | ❌ **NÃO** (Virou Objeto com `display_value` e `link`) | `SEVERIDADE` |
| `source_table` | `source_table` | `source_table` | `source_table` | `str` | `str` | **Sim** (`'sn_customerservice_case'`) | **Sim** (String direta) | `TABELA_FONTE` |
| `source_record.number` | `source_record` | `source_record.number` | `source_record` | `str` | `dict` | ⚠️ **DIFERENTE** (Veja detalhe abaixo) | ❌ **NÃO** (Virou Objeto com `display_value` e `link`) | `ID` |
| `opened_at` | `opened_at` | `opened_at` | `opened_at` | `str` | `str` | **Sim** (`'DD/MM/YYYY HH:MM:SS'`) | **Sim** (String data/hora) | `ABERTURA_HORA` |
| `assignment_group.name` | `assignment_group` | `assignment_group.name` | `assignment_group` | `str` | `dict` | **Sim** (ex: `'GP055 (Atendimento)'`) | ❌ **NÃO** (Virou Objeto com `display_value` e `link`) | `GRUPO` |
| `assigned_to.name` | `assigned_to` | `assigned_to.name` | `assigned_to` | `str` | `dict` ou `""` | **Sim** (Nome do técnico) | ❌ **NÃO** (Virou Objeto com `display_value` e `link`) | `TECNICO_NOME` |
| `sys_updated_on` | `sys_updated_on` | `sys_updated_on` | `sys_updated_on` | `str` | `str` | **Sim** (`'DD/MM/YYYY HH:MM:SS'`) | **Sim** (String data/hora) | `ATUALIZACAO_HORA` |
| `de_escalated_by.name` | `de_escalated_by` | `de_escalated_by.name` | `de_escalated_by` | `str` | `dict` ou `""` | **Sim** (Nome do técnico) | ❌ **NÃO** (Virou Objeto com `display_value` e `link`) | `TECNICO_ESC` |
| `closed_at` | `closed_at` | `closed_at` | `closed_at` | `str` | `str` | **Sim** (`'DD/MM/YYYY HH:MM:SS'`) | **Sim** (String data/hora) | `FECHAMENTO_HORA` |

---

## 4. Comparação Lado a Lado dos Dados Brutos

### Registro no Padrão Anterior:
```json
{
  "number": "ESC0037367",
  "opened_at": "07/05/2024 09:27:01",
  "closed_at": "27/02/2026 11:32:03",
  "state": "Encerrado",
  "sys_updated_on": "27/02/2026 11:32:03",
  "source_table": "sn_customerservice_case",
  "escalation_severity.name": "Gravidade alta",
  "source_record.number": "CS0211993",
  "assignment_group.name": "GP061 (Atendimento)",
  "assigned_to.name": "Humaita Vieira Schiavinoto",
  "de_escalated_by.name": "Humaita Vieira Schiavinoto"
}
```

### Registro no Novo Padrão Gerado:
```json
{
  "number": "ESC0153172",
  "opened_at": "27/10/2026 15:29:15",
  "closed_at": "27/10/2026 15:42:04",
  "state": "Encerrado",
  "sys_updated_on": "27/10/2026 15:42:04",
  "source_table": "sn_customerservice_case",
  "escalation_severity": {
    "display_value": "Gravidade alta",
    "link": "https://dataprev.service-now.com/api/now/table/sn_customerservice_escalation_severity/3fd67373b3500300ff6e6e5f26a8dcd4"
  },
  "source_record": {
    "display_value": "Solicitação: CS0721537",
    "link": "https://dataprev.service-now.com/api/now/table/sn_customerservice_case/5acb6bbffb801750adebf7b86eefdcbb"
  },
  "assignment_group": {
    "display_value": "GP055 (Atendimento)",
    "link": "https://dataprev.service-now.com/api/now/table/sys_user_group/98db5fe687e5d9504d40c8880cbb35fd"
  },
  "assigned_to": {
    "display_value": "Luiz Paulo Fernandes da Fonseca",
    "link": "https://dataprev.service-now.com/api/now/table/sys_user/7786031d1b82d11088e6da02f54bcb43"
  },
  "de_escalated_by": {
    "display_value": "Luiz Paulo Fernandes da Fonseca",
    "link": "https://dataprev.service-now.com/api/now/table/sys_user/7786031d1b82d11088e6da02f54bcb43"
  }
}
```

---

## 5. Análise do ETL: O Código Funcionaria Somente com o Escalonamento?

### Resposta Direta: **NÃO FUNCIONA.**

Se o pipeline do projeto for executado (`python3 main.py` ou `python3 etl_transformacao_dados.py`), ele **falhará imediatamente com erro fatal (`sys.exit(1)`)** ao tentar processar o escalonamento.

### Diagnóstico Técnico Passo a Passo:

#### 1. Falha no `pd.json_normalize` e Descasamento no `DEPARA_COLUNAS`
No script `etl_transformacao_dados.py`, a função `transformar_json_em_dataframe()` executa:
```python
df = pd.json_normalize(self.dados_brutos)
```
Como os 5 campos de referência agora são dicionários, o `pd.json_normalize` cria colunas aninhadas com sufixo `.display_value` e `.link`:
*   `escalation_severity.display_value` e `escalation_severity.link`
*   `source_record.display_value` e `source_record.link`
*   `assignment_group.display_value` e `assignment_group.link`
*   `assigned_to.display_value` e `assigned_to.link`
*   `de_escalated_by.display_value` e `de_escalated_by.link`

Porém, no [`config.yml`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/config.yml#L134-L145), o mapeamento de colunas (`DEPARA_COLUNAS`) espera os nomes antigos:
```yaml
escalonamento:
  number: "ID_ESC"
  state: "STATUS"
  escalation_severity.name: "SEVERIDADE"  # <-- NÃO EXISTE NO DATAFRAME
  source_table: "TABELA_FONTE"
  source_record.number: "ID"              # <-- NÃO EXISTE NO DATAFRAME
  opened_at: "ABERTURA_HORA"
  assignment_group.name: "GRUPO"          # <-- NÃO EXISTE NO DATAFRAME
  assigned_to.name: "TECNICO_NOME"        # <-- NÃO EXISTE NO DATAFRAME
  sys_updated_on: "ATUALIZACAO_HORA"
  de_escalated_by.name: "TECNICO_ESC"     # <-- NÃO EXISTE NO DATAFRAME
  closed_at: "FECHAMENTO_HORA"
```
Como o loop `df.rename(columns=...)` não encontra essas chaves, as 5 colunas de destino (`SEVERIDADE`, `ID`, `GRUPO`, `TECNICO_NOME` e `TECNICO_ESC`) **nunca são criadas**.

#### 2. Queda Fatal com `KeyError` no `ordenar_colunas()`
Logo após renomear, o script chama `tabela.ordenar_colunas()`. Essa função tenta selecionar a lista definida em `ORDEM_COLUNAS`:
```python
df = df[['ID_ESC', 'STATUS', 'SEVERIDADE', 'TABELA_FONTE', 'ID', 'ABERTURA_HORA', 'GRUPO', 'TECNICO_NOME', 'ATUALIZACAO_HORA', 'TECNICO_ESC', 'FECHAMENTO_HORA']]
```
Como 5 dessas colunas não existem no DataFrame, o Pandas lança a exceção:
```text
KeyError: "['SEVERIDADE', 'ID', 'GRUPO', 'TECNICO_NOME', 'TECNICO_ESC'] not in index"
```
O bloco `except` captura o erro, emite no log `Falha na ordenação de colunas.` e chama `sys.exit(1)`. O CSV processado **não é gerado**.

---

## 6. Efeitos Colaterais Subsequentes (Carga no Banco e Snapshots)

Mesmo que o `DEPARA_COLUNAS` fosse alterado para usar `.display_value`, ainda haveria **quebras graves no banco de dados e relatórios**:

### 1. Alteração do Conteúdo de `source_record` (`ID`)
*   **Antes (`source_record.number`):** O valor era puramente o código do chamado: `"CS0211993"`.
*   **Novo (`source_record.display_value`):** O ServiceNow formata o display com prefixo textual: `"Solicitação: CS0721537"`.

### 2. Quebra nas Regras de Negócio e Snapshots em SQL ([`queries.yml`](file:///home/geral/PythonScripts/AtualizaAtendimento.v2/queries.yml#L75-L77))
O cálculo da tabela `SnapshotESC` depende estritamente dos dois primeiros caracteres do campo `ID`:
```sql
REPLACE INTO SnapshotESC select CURDATE() as EXTRACAO_DT, year(ABERTURA_HORA) AS ABERTURA_ANO,
IF (SUBSTR(ID,1,2)='TI','S','N') AS PTI, STATUS, GRUPO, TECNICO_NOME, count(ID) as TIQUET_QTD
from Escalonamento group by EXTRACAO_DT, ABERTURA_ANO, PTI, STATUS, GRUPO, TECNICO_NOME;
```
Com o novo valor (`"Solicitação: CS..."`), `SUBSTR(ID, 1, 2)` retornará `"So"`. Dessa forma, **nenhum registro será reconhecido como PTI**, distorcendo os indicadores de atendimento.

### 3. Risco de Truncamento no MariaDB
Na query `PRONTO_ESCALONAMENTO`:
```sql
LOAD DATA LOCAL INFILE '{0}' REPLACE INTO TABLE Escalonamento ... (ID_ESC, STATUS, SEVERIDADE, TABELA_FONTE, ID, ...)
```
Se a coluna `ID` na tabela MariaDB tiver tamanho limitado (ex: `VARCHAR(10)` ou `VARCHAR(15)`), `"Solicitação: CS0721537"` (23 caracteres) causará erro de inserção ou truncamento de dados.

---

## 7. Recomendações e Conclusão

1. **Se a intenção era manter o fluxo atual funcionando perfeitamente:**
   A configuração em `SERVICENOW -> parametros -> sysparm_fields -> escalonamento` no `config.yml` deve voltar a utilizar a notação com `.name` e `.number`:
   ```yaml
   escalonamento: "number,state,escalation_severity.name,source_table,source_record.number,opened_at,assignment_group.name,assigned_to.name,sys_updated_on,de_escalated_by.name,closed_at"
   ```
   Dessa forma, a API do ServiceNow já entrega os valores pré-resolvidos em formato texto simples, garantindo compatibilidade total com o `etl_transformacao_dados.py`, `queries.yml` e o banco MariaDB.

2. **Se você realmente deseja usar a nova estrutura de dados (com objetos e links):**
   Será necessário:
   * Ajustar o `DEPARA_COLUNAS` no `config.yml` para apontar para `*.display_value`.
   * Incluir um tratamento específico na transformação para retirar o prefixo `"Solicitação: "` do campo `source_record.display_value`, isolando apenas o código do chamado (ex: `CS0721537`).

