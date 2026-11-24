# Escalonamento — Impacto no ETL e Banco

**Relacionado:** [[Escalonamento - Consulta e Mudanças no config.yml]] | [[Índice - Testes ServiceNow (Mateus)]]

---

## O Código do ETL Funcionaria Somente com o Escalonamento?

### ❌ **NÃO FUNCIONA.**

1. **Quebra na Transformação (`etl_transformacao_dados.py`):**
   * O `pd.json_normalize()` expande os novos dicionários para colunas com sufixo `.display_value` e `.link` (ex: `assignment_group.display_value`).
   * O `DEPARA_COLUNAS` no `config.yml` ainda procura pelas chaves antigas (`assignment_group.name`, `source_record.number`, etc.).
   * Resultado: `SEVERIDADE`, `ID`, `GRUPO`, `TECNICO_NOME` e `TECNICO_ESC` não são criadas, e `ordenar_colunas()` falha com:
     ```text
     KeyError: "['SEVERIDADE', 'ID', 'GRUPO', 'TECNICO_NOME', 'TECNICO_ESC'] not in index"
     ```
     encerrando o script com `sys.exit(1)`.

2. **Quebra na Regra de Negócio / SQL (`queries.yml`):**
   * Mesmo se o `DEPARA_COLUNAS` fosse ajustado para `.display_value`, o campo `ID` passaria a receber `"Solicitação: CS0721537"` em vez de `"CS0721537"`.
   * Na query `SnapshotESC` do `queries.yml`:
     ```sql
     IF (SUBSTR(ID,1,2)='TI','S','N') AS PTI
     ```
     `SUBSTR(ID, 1, 2)` passaria a ler `"So"`, quebrando a classificação de PTI (além de risco de truncamento no MariaDB).

---

## Como Corrigir
* **Opção recomendada:** voltar os campos no `config.yml` para `.name` e `.number` (compatibilidade 100% com ETL e SQL).
* **Alternativa:** mapear `*.display_value` no `DEPARA_COLUNAS` e remover o prefixo `"Solicitação: "` no tratamento do `ID`.
