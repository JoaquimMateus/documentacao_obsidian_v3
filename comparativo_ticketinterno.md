## Relatório Comparativo: Ticket Interno (Arquivo do ETL vs. Retorno do Postman) & Impacto no ETL

**Data da análise:** 25/11/2026  
**Arquivos comparados:**
- **Base processada/consulta direcionada:** `ticket_interno.json`
- **Retorno bruto do Postman:** `ticket_interno_postman.json`

> **Premissa:** `ticket_interno.json` representa o formato atualmente esperado pelo fluxo, com campos direcionados, valores de exibição resolvidos por dot-walking e datas convertidas para o horário local. O arquivo do Postman representa uma consulta ampla, com referências retornadas como objetos técnicos.

### 1. Resumo executivo

| Métrica / parâmetro | `ticket_interno.json` | `ticket_interno_postman.json` |
|---|---:|---:|
| Registros | **1.259** | **2** |
| Tamanho | **1.106.540 bytes** (~1,06 MB) | **12.218 bytes** (~11,9 KB) |
| Campos no primeiro registro | **20** | **98** |
| Prefixo observado | `PTI` | `PTI` |
| Formato das referências | Strings resolvidas por dot-walking | Objetos `{link, value}` com `sys_id` |
| Estado | Texto de exibição, ex.: `Fechado` | Código interno, ex.: `3` |
| Datas | `DD/MM/AAAA HH:MM:SS`, horário local | `AAAA-MM-DD HH:MM:SS`, aparentemente UTC |
| Registros comuns | **Nenhum** | **Nenhum** |

**Conclusão direta:** o retorno bruto do Postman não é estruturalmente compatível com o arquivo consumido pelo ETL. Ele entrega códigos internos e objetos de referência no lugar dos nomes e descrições exigidos pelo fluxo.

### 2. Campos utilizados na base direcionada

O arquivo processado possui exatamente estes 20 campos:

```text
number
state
parent
opened_at
u_resolved_at
closed_at
sys_updated_on
u_requested_for.name
assigned_to.name
assignment_group.name
u_category.sc_catalog.title
u_category.title
u_catalog_item.name
u_functionality.u_functionality
u_module.u_module
u_reason.u_name
u_reassigned
u_reassignment_count
u_reclassified
u_reclassification_count
```

O Postman retorna 98 campos. A maior parte não é utilizada pelo destino, enquanto os campos de referência necessários aparecem como objetos com `link` e `value`.

### 3. Matriz DE-PARA dos campos do Ticket Interno

| Campo esperado no arquivo atual | Campo bruto no Postman | Tipo atual | Tipo Postman | Informação equivalente? | Compatível sem tratamento? |
|---|---|---|---|---|---|
| `number` | `number` | `str` | `str` | Sim | **Sim** |
| `state` | `state` | Texto, ex.: `Fechado` | Código, ex.: `3` | Parcial; exige dicionário de escolhas | **Não** |
| `parent` | `parent` | String vazia ou valor esperado pelo fluxo | String vazia ou objeto de referência | Depende do registro | **Não**, se preenchido como objeto |
| `opened_at` | `opened_at` | Data local formatada | Data ISO-like, aparentemente UTC | Sim, após conversão | **Não** |
| `u_resolved_at` | `u_resolved_at` | Data local formatada | Data ISO-like, aparentemente UTC | Sim, após conversão | **Não** |
| `closed_at` | `closed_at` | Data local formatada | Data ISO-like, aparentemente UTC | Sim, após conversão | **Não** |
| `sys_updated_on` | `sys_updated_on` | Data local formatada | Data ISO-like, aparentemente UTC | Sim, após conversão | **Não** |
| `u_requested_for.name` | `u_requested_for` | Nome do solicitante | `{link, value}` com `sys_id` | Só após resolver a referência | **Não** |
| `assigned_to.name` | `assigned_to` | Nome do técnico | `{link, value}` com `sys_id` | Só após resolver a referência | **Não** |
| `assignment_group.name` | `assignment_group` | Nome do grupo | `{link, value}` com `sys_id` | Só após resolver a referência | **Não** |
| `u_category.sc_catalog.title` | `u_category` | Título do catálogo | Objeto da categoria | Exige navegação adicional pela categoria e catálogo | **Não** |
| `u_category.title` | `u_category` | Título da categoria | Objeto com `sys_id` | Só após resolver a referência | **Não** |
| `u_catalog_item.name` | `u_catalog_item` | Nome do item de catálogo | Objeto com `sys_id` | Só após resolver a referência | **Não** |
| `u_functionality.u_functionality` | `u_functionality` | Nome da funcionalidade | Objeto com `sys_id` | Só após resolver a referência | **Não** |
| `u_module.u_module` | `u_module` | Nome do módulo | Objeto com `sys_id` | Só após resolver a referência | **Não** |
| `u_reason.u_name` | `u_reason` | Motivo descritivo | Objeto com `sys_id` | Só após resolver a referência | **Não** |
| `u_reassigned` | `u_reassigned` | Texto | Texto | Sim | **Sim** |
| `u_reassignment_count` | `u_reassignment_count` | String numérica | String numérica | Sim | **Sim** |
| `u_reclassified` | `u_reclassified` | Texto | Texto | Sim | **Sim** |
| `u_reclassification_count` | `u_reclassification_count` | String numérica | String numérica | Sim | **Sim** |

### 4. Comparação lado a lado dos dados brutos

Não há registros comuns entre os arquivos. Portanto, os exemplos abaixo demonstram a diferença estrutural, não uma comparação de conteúdo do mesmo ticket.

#### Registro no padrão esperado pelo ETL

```json
{
  "number": "PTI0165681",
  "state": "Fechado",
  "parent": "",
  "opened_at": "21/10/2026 15:42:32",
  "u_resolved_at": "21/10/2026 16:13:42",
  "closed_at": "28/10/2026 23:30:25",
  "sys_updated_on": "28/10/2026 23:30:25",
  "u_requested_for.name": "Renato Giovani Barroso",
  "assigned_to.name": "Pierry Rodrigues da Silveira",
  "assignment_group.name": "N1.GP077 ",
  "u_category.sc_catalog.title": "TI Interna",
  "u_category.title": "SOFTWARE BÁSICO",
  "u_catalog_item.name": "Aplicativos",
  "u_functionality.u_functionality": "Atualização",
  "u_module.u_module": "Softwares padrão",
  "u_reason.u_name": "Solicitação de Serviço (Requisição)",
  "u_reassigned": "Não",
  "u_reassignment_count": "0",
  "u_reclassified": "Não",
  "u_reclassification_count": "0"
}
```

#### Registro no padrão bruto do Postman

```json
{
  "number": "PTI0164643",
  "state": "3",
  "parent": "",
  "opened_at": "2026-10-08 17:56:38",
  "u_resolved_at": "2026-10-08 18:54:55",
  "closed_at": "2026-10-16 02:30:21",
  "sys_updated_on": "2026-10-16 02:30:21",
  "u_requested_for": {
    "link": ".../api/now/table/sys_user/ef96cd411b78c550b120eb9fe54bcb2a",
    "value": "ef96cd411b78c550b120eb9fe54bcb2a"
  },
  "assigned_to": {
    "link": ".../api/now/table/sys_user/a9bba4071bb8451088e6da02f54bcb11",
    "value": "a9bba4071bb8451088e6da02f54bcb11"
  },
  "assignment_group": {
    "link": ".../api/now/table/sys_user_group/c94a3f0e1b28c9503f37a750f54bcb1e",
    "value": "c94a3f0e1b28c9503f37a750f54bcb1e"
  },
  "u_reason": {
    "link": ".../api/now/table/u_reasons/f2e607b11b4c8d503f37a750f54bcbac",
    "value": "f2e607b11b4c8d503f37a750f54bcbac"
  }
}
```

### 5. Estados e valores de exibição

Na base direcionada, foram encontrados os seguintes estados textuais:

| Estado | Quantidade |
|---|---:|
| Fechado | 1.198 |
| Em andamento | 19 |
| Aguardando fornecedor | 18 |
| Cancelado | 8 |
| Aberto | 6 |
| Rejeitado | 4 |
| Aguardando posição do usuário final | 2 |
| Em análise | 2 |
| Aguardando suporte ao atendimento | 2 |

No Postman, os dois exemplos apresentam `state = "3"`. Esse código não deve ser gravado diretamente em uma coluna que atualmente recebe descrições como `Fechado`, `Em andamento` ou `Cancelado`.

### 6. Diferença de formato e fuso das datas

Os arquivos não possuem tickets em comum, então não é possível demonstrar matematicamente a diferença de três horas usando o mesmo registro. Ainda assim, o padrão é consistente com o observado no Ticket Externo:

- Arquivo processado: `DD/MM/AAAA HH:MM:SS`.
- Postman: `AAAA-MM-DD HH:MM:SS`.
- Retorno do Postman aparenta estar em UTC, enquanto a base processada aparenta utilizar horário local UTC-3.

**Recomendação:** confirmar o fuso com uma consulta do mesmo `number` nos dois métodos antes de fixar a conversão. Não se deve subtrair três horas apenas com base em registros diferentes.

### 7. Análise do ETL

#### Resposta direta: **o JSON bruto do Postman não funciona diretamente no ETL atual.**

Ao executar `pd.json_normalize()` no retorno bruto, serão criadas colunas como:

```text
u_requested_for.link
u_requested_for.value
assigned_to.link
assigned_to.value
assignment_group.link
assignment_group.value
u_category.link
u_category.value
u_catalog_item.link
u_catalog_item.value
u_functionality.link
u_functionality.value
u_module.link
u_module.value
u_reason.link
u_reason.value
```

Porém, o fluxo atual espera:

```text
u_requested_for.name
assigned_to.name
assignment_group.name
u_category.sc_catalog.title
u_category.title
u_catalog_item.name
u_functionality.u_functionality
u_module.u_module
u_reason.u_name
```

Essas nove colunas não serão geradas pelo `json_normalize()` do Postman. Se o `DEPARA_COLUNAS` e a ordenação mantiverem os nomes atuais, o processamento poderá terminar com `KeyError` e impedir a geração do CSV.

#### Riscos adicionais

1. **Solicitante, técnico e grupo viram `sys_id`**, e não nomes legíveis.
2. **Categoria, catálogo, item, funcionalidade, módulo e motivo perdem a descrição de negócio.**
3. **O estado passa de texto para código**, quebrando agrupamentos e relatórios que esperam descrições.
4. **Datas mudam de formato e possivelmente de fuso**, afetando duração, fechamento diário e snapshots.
5. **`parent` é heterogêneo:** pode ser `""` ou objeto. Isso produz colunas diferentes entre registros após a normalização.
6. **98 campos são transferidos para usar apenas 20**, elevando tráfego e custo de transformação sem benefício para o destino atual.

### 8. Configuração recomendada

Para manter compatibilidade com o arquivo atual, a consulta deve restringir os campos e resolver os valores por dot-walking:

```text
sysparm_display_value=true
sysparm_exclude_reference_link=true
sysparm_fields=number,state,parent,opened_at,u_resolved_at,closed_at,sys_updated_on,u_requested_for.name,assigned_to.name,assignment_group.name,u_category.sc_catalog.title,u_category.title,u_catalog_item.name,u_functionality.u_functionality,u_module.u_module,u_reason.u_name,u_reassigned,u_reassignment_count,u_reclassified,u_reclassification_count
```

**Ponto de validação:** se `parent` precisar representar número ou outro atributo do ticket pai, substitua o campo simples pelo dot-walking correto, por exemplo `parent.number`. Na amostra processada, os 1.259 valores de `parent` estão vazios, então esse comportamento não pôde ser validado.

### 9. Caso seja obrigatório usar o retorno bruto

Será necessário:

1. Resolver `state` por meio das opções da tabela `u_pronto_request_internal_it`.
2. Consultar usuários para obter `u_requested_for.name` e `assigned_to.name`.
3. Consultar `sys_user_group` para obter `assignment_group.name`.
4. Consultar `sc_category` para obter `u_category.title` e seguir a referência do catálogo para `u_category.sc_catalog.title`.
5. Consultar `sc_cat_item_producer`, `sn_customerservice_functionality`, `sn_customerservice_module` e `u_reasons` para recuperar os textos esperados.
6. Normalizar `parent`, tratando separadamente string vazia e objeto de referência.
7. Converter datas para o fuso adotado pelo banco e aplicar `%d/%m/%Y %H:%M:%S`.
8. Recriar as 20 colunas esperadas antes da renomeação, ordenação e carga.

Essa abordagem exige várias consultas auxiliares ou cache de referências, aumenta a latência e pode atingir limites de API. Para o fluxo atual, o dot-walking no `sysparm_fields` é mais simples e seguro.

### 10. Conclusão

O `ticket_interno.json` contém dados de negócio já resolvidos e prontos para o ETL. O `ticket_interno_postman.json` contém a versão técnica bruta, com códigos, `sys_id` e links. A recomendação é manter os 20 campos direcionados, `sysparm_display_value=true` e dot-walking. O retorno bruto só deve ser adotado se o ETL ganhar uma etapa explícita de enriquecimento, conversão de estados, normalização de referências e tratamento de fuso.