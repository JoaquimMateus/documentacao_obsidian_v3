## Relatório Comparativo: Ticket Externo (Arquivo do ETL vs. Retorno do Postman) & Impacto no ETL

**Data da análise:** 25/11/2026  
**Arquivos comparados:**
- **Base processada/consulta direcionada:** `ticket_externo.json`
- **Retorno bruto do Postman:** `ticket_externo_postman.json`

> **Premissa:** `ticket_externo.json` representa o formato atualmente esperado pelo fluxo, com `sysparm_fields` direcionado, `sysparm_display_value=true` e datas ajustadas para o horário local. O arquivo do Postman representa uma consulta ampla, sem a mesma resolução de valores de exibição.

### 1. Resumo executivo

| Métrica / parâmetro | `ticket_externo.json` | `ticket_externo_postman.json` |
|---|---:|---:|
| Registros | **983** | **3** |
| Tamanho | **891.675 bytes** (~871 KB) | **24.832 bytes** (~24 KB) |
| Campos no primeiro registro | **22** | **139** |
| Formato das referências | Strings resolvidas por dot-walking | Objetos `{link, value}` com `sys_id` |
| Estado | Texto de exibição, ex.: `Encerrado` | Código interno, ex.: `3` |
| Datas | `DD/MM/AAAA HH:MM:SS` em horário local | `AAAA-MM-DD HH:MM:SS`, aparentemente UTC |
| Escopo observado | 700 tickets `CS` e 283 `CSP` | 2 tickets `CS` e 1 `CSP` |
| Registro comum entre os arquivos | `CSP0001253` | `CSP0001253` |

**Conclusão direta:** os arquivos não são equivalentes em estrutura nem em semântica. O retorno do Postman, se entregue diretamente ao ETL atual, tende a quebrar o mapeamento de colunas e, mesmo após um simples `json_normalize`, gravaria códigos internos (`sys_id`) no lugar dos nomes esperados.

### 2. Campos utilizados na base direcionada

O arquivo processado possui exatamente estes 22 campos:

```text
number
state
parent.number
opened_at
first_response_time
resolved_at
closed_at
sys_updated_on
assigned_to.name
assignment_group.name
contact.name
account.name
u_category.sc_catalog.title
u_category.title
u_catalog_item.name
u_functionality.u_functionality
u_module.u_module
u_symptoms
u_reassigned
u_reassignment_count
u_reclassified
u_reclassification_count
```

O Postman retorna 139 campos, mas os campos de referência são apresentados no nível raiz e como objetos. Isso aumenta o volume estrutural sem fornecer diretamente os textos exigidos pelo destino.

### 3. Matriz DE-PARA dos campos do Ticket Externo

| Campo esperado no arquivo atual | Campo bruto no Postman | Tipo atual | Tipo Postman | Informação equivalente? | Compatível sem tratamento? |
|---|---|---|---|---|---|
| `number` | `number` | `str` | `str` | Sim | **Sim** |
| `state` | `state` | Texto (`Encerrado`) | Código (`3`) | Parcial, depende do dicionário de escolhas | **Não** |
| `parent.number` | `parent` | Número do ticket pai | Objeto com `sys_id` | Não diretamente | **Não** |
| `opened_at` | `opened_at` | Data local formatada | Data ISO-like, aparentemente UTC | Sim, após conversão | **Não** |
| `first_response_time` | `first_response_time` | Data local formatada | Data ISO-like, aparentemente UTC | Sim, após conversão | **Não** |
| `resolved_at` | `resolved_at` | Data local formatada | Data ISO-like, aparentemente UTC | Sim, após conversão | **Não** |
| `closed_at` | `closed_at` | Data local formatada | Data ISO-like, aparentemente UTC | Sim, após conversão | **Não** |
| `sys_updated_on` | `sys_updated_on` | Data local formatada | Data ISO-like, aparentemente UTC | Sim, após conversão | **Não** |
| `assigned_to.name` | `assigned_to` | Nome do técnico | `{link, value}` com `sys_id` | Só consultando a referência ou usando display value | **Não** |
| `assignment_group.name` | `assignment_group` | Nome do grupo | `{link, value}` com `sys_id` | Só consultando a referência ou usando display value | **Não** |
| `contact.name` | `contact` | Nome do contato | `{link, value}` com `sys_id` | Só consultando a referência ou usando display value | **Não** |
| `account.name` | `account` | Nome da conta | `{link, value}` com `sys_id` | Só consultando a referência ou usando display value | **Não** |
| `u_category.sc_catalog.title` | `u_category` | Título do catálogo | Objeto da categoria | Não diretamente; exige novo lookup/dot-walking | **Não** |
| `u_category.title` | `u_category` | Título da categoria | Objeto com `sys_id` | Só consultando a referência ou usando display value | **Não** |
| `u_catalog_item.name` | `u_catalog_item` | Nome do item | Objeto com `sys_id` | Só consultando a referência ou usando display value | **Não** |
| `u_functionality.u_functionality` | `u_functionality` | Nome da funcionalidade | Objeto com `sys_id` | Só consultando a referência ou usando display value | **Não** |
| `u_module.u_module` | `u_module` | Nome do módulo | Objeto com `sys_id` | Só consultando a referência ou usando display value | **Não** |
| `u_symptoms` | `u_symptoms` | Código/texto | Código/texto | Sim | **Sim** |
| `u_reassigned` | `u_reassigned` | Texto | Texto | Sim | **Sim** |
| `u_reassignment_count` | `u_reassignment_count` | String numérica | String numérica | Sim | **Sim** |
| `u_reclassified` | `u_reclassified` | Texto | Texto | Sim | **Sim** |
| `u_reclassification_count` | `u_reclassification_count` | String numérica | String numérica | Sim | **Sim** |

### 4. Comparação do mesmo registro: `CSP0001253`

Esse foi o único ticket presente nos dois arquivos e permite verificar as diferenças sem depender de registros distintos.

#### Formato esperado pelo arquivo atual

```json
{
  "number": "CSP0001253",
  "state": "Encerrado",
  "opened_at": "09/11/2026 11:35:54",
  "sys_updated_on": "20/11/2026 23:30:28",
  "assigned_to.name": "Vanderleia Rodrigues Silva Santos",
  "assignment_group.name": "N1.Previdência-Benefício",
  "contact.name": "Julio Cesar Alves Xavier",
  "account.name": "INSS",
  "u_category.sc_catalog.title": "INSS",
  "u_category.title": "PRISMA",
  "u_catalog_item.name": "PRISMA D3",
  "u_functionality.u_functionality": "N/A",
  "u_module.u_module": "Benefícios"
}
```

#### Formato bruto retornado no Postman

```json
{
  "number": "CSP0001253",
  "state": "3",
  "opened_at": "2026-11-09 14:35:54",
  "sys_updated_on": "2026-11-21 02:30:28",
  "assigned_to": {
    "link": ".../api/now/table/sys_user/d642dc001bba8990b120eb9fe54bcb59",
    "value": "d642dc001bba8990b120eb9fe54bcb59"
  },
  "assignment_group": {
    "link": ".../api/now/table/sys_user_group/5fa05d2a1b39205088e6da02f54bcb26",
    "value": "5fa05d2a1b39205088e6da02f54bcb26"
  },
  "account": {
    "link": ".../api/now/table/customer_account/99daa0bf1be668107592da8ee54bcb91",
    "value": "99daa0bf1be668107592da8ee54bcb91"
  }
}
```

### 5. Diferença de fuso e formato das datas

No registro comum, as datas do Postman estão exatamente **3 horas à frente** das datas do arquivo processado:

| Campo | Arquivo atual | Postman | Diferença |
|---|---|---|---:|
| `opened_at` | `09/11/2026 11:35:54` | `2026-11-09 14:35:54` | +3 h |
| `sys_updated_on` | `20/11/2026 23:30:28` | `2026-11-21 02:30:28` | +3 h |
| `closed_at` | `20/11/2026 23:30:28` | `2026-11-21 02:30:28` | +3 h |

Isso é consistente com retorno em UTC convertido para `America/Sao_Paulo`/UTC-3 na base processada. A conversão precisa ocorrer antes da carga, inclusive quando a subtração altera o dia, como em `21/11 02:30:28` para `20/11 23:30:28`.

### 6. Impacto no ETL atual

#### Resposta direta: **o JSON bruto do Postman não é compatível com o ETL esperado.**

Ao executar `pd.json_normalize()` no retorno bruto, seriam criadas colunas como:

```text
assigned_to.link
assigned_to.value
assignment_group.link
assignment_group.value
contact.link
contact.value
account.link
account.value
u_category.link
u_category.value
u_catalog_item.link
u_catalog_item.value
u_functionality.link
u_functionality.value
u_module.link
u_module.value
parent.link
parent.value
```

Porém, o arquivo atual depende de colunas como `assigned_to.name`, `assignment_group.name`, `account.name`, `u_category.title` e `parent.number`. Essas colunas não surgem no `json_normalize()` do retorno do Postman.

#### Falhas e riscos principais

1. **`KeyError` na seleção/ordenação de colunas** se o ETL exigir os 22 nomes atuais.
2. **Carga de `sys_id` em vez de descrições**, caso o DE-PARA seja alterado apenas de `.name` para `.value`.
3. **Perda do estado textual**, pois `3` não equivale diretamente a `Encerrado` sem dicionário de choices.
4. **Datas deslocadas em 3 horas** e em formato incompatível com o padrão atual.
5. **Perda do número do ticket pai**, porque `parent.value` é o `sys_id`, não `parent.number`.
6. **Perda do catálogo da categoria**, pois `u_category.sc_catalog.title` exige navegação adicional além da referência de `u_category`.
7. **Aumento desnecessário da superfície de dados**, com 139 campos retornados para apenas 22 efetivamente usados.

### 7. Configuração recomendada para manter o fluxo atual

A consulta deve continuar restringindo os campos e solicitando os valores resolvidos:

```text
sysparm_display_value=true
sysparm_exclude_reference_link=true
sysparm_fields=number,state,parent.number,opened_at,first_response_time,resolved_at,closed_at,sys_updated_on,assigned_to.name,assignment_group.name,contact.name,account.name,u_category.sc_catalog.title,u_category.title,u_catalog_item.name,u_functionality.u_functionality,u_module.u_module,u_symptoms,u_reassigned,u_reassignment_count,u_reclassified,u_reclassification_count
```

**Observação importante:** valide no ambiente de homologação se `sysparm_exclude_reference_link=true` preserva os campos obtidos por dot-walking. O objetivo é remover links de referência sem perder os textos já resolvidos.

### 8. Caso seja obrigatório usar o retorno bruto

Será necessário implementar, no mínimo:

1. Resolver `state=3` para o display correspondente na tabela/choices do ServiceNow.
2. Consultar as referências de usuário, grupo, contato, conta, categoria, item, funcionalidade e módulo para converter `sys_id` em texto.
3. Consultar o `parent.value` na tabela `task` para obter `parent.number`.
4. Consultar a categoria e depois o catálogo para obter `u_category.sc_catalog.title`.
5. Converter datas de UTC para `America/Sao_Paulo` e formatar como `%d/%m/%Y %H:%M:%S`.
6. Recriar exatamente as 22 colunas esperadas antes da ordenação e carga.

Essa alternativa é mais lenta, aumenta a quantidade de chamadas à API e cria risco de rate limiting. Para este fluxo, o dot-walking na própria consulta é tecnicamente mais simples e mais aderente ao JSON já consumido.

### 9. Conclusão

A diferença não está apenas na quantidade de campos. O arquivo atual contém **dados de negócio prontos para carga**, enquanto o Postman contém **identificadores técnicos e metadados brutos**. A recomendação é manter a consulta direcionada com os 22 campos, `sysparm_display_value=true` e dot-walking. Usar diretamente o retorno amplo do Postman exigiria uma nova etapa de enriquecimento e conversão antes do ETL.