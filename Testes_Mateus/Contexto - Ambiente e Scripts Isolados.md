# Contexto — Ambiente e Scripts Isolados

**Diretório dos testes:** `/home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus/`  
**Regra:** Os arquivos originais do projeto (`config.yml`, `etl_consulta_api.py`, `etl_transformacao_dados.py`, `queries.yml`) permanecem **intocados**.

---

## Scripts Criados

| Script | Tabela / Endpoint | JSON Gerado |
| :--- | :--- | :--- |
| `consulta_escalonamento.py` | `sn_customerservice_escalation` | `escalonamento.json` |
| `consulta_pesquisa_pronto.py` | `asmt_metric_result` | `pesquisa_pronto.json` |
| `consulta_incidente.py` | `incident` | `incidente.json` |

### Como Executar
Todos os scripts leem o `config.yml` original em modo somente leitura e aceitam `--delta <dias>`, `--sem-filtro-data`, `--limite-maximo <n>` e `--output <caminho>`:
```bash
python3 /home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus/consulta_escalonamento.py
python3 /home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus/consulta_pesquisa_pronto.py
python3 /home/geral/PythonScripts/AtualizaAtendimento.v2/TesteV3/Teste_Mateus/consulta_incidente.py
```

---

## Nota Técnica de Ambiente (Python 3.11 vs. venv 3.7)
* O sistema usa **Python 3.11**, que já possui `pandas` e `PyYAML` instalados globalmente, mas não possui `requests`.
* O `venv` do projeto foi criado em **Python 3.7** (contém `requests` puro Python, mas binários de `numpy`/`pandas` incompatíveis com 3.11).
* **Solução aplicada:** Os scripts de consulta adicionam `venv/lib/python3.7/site-packages` ao `sys.path` para importar `requests` sem depender de `pandas`. Simulações com `pandas` devem usar `python3` puro do sistema.

**Voltar:** [[Índice - Testes ServiceNow (Mateus)]]
