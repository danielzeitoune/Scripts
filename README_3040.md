# CADOC 3040 — Pipeline de Geração XML Regulatório

Pipeline em Python para geração automática do **CADOC 3040**, relatório obrigatório de operações de crédito exigido pelo Banco Central do Brasil (BACEN).

## Sobre o Projeto

O **CADOC 3040** (Documento de Crédito) é uma obrigação regulatória mensal para instituições financeiras. Este pipeline automatiza a extração, transformação e geração do arquivo XML no formato exigido pelo BACEN.

## Stack Utilizada

| Tecnologia | Uso |
|---|---|
| **Python 3.12** |Linguagem principal|
| **Google BigQuery** | Data warehouse |
| **pandas** | Manipulação |
| **xml.etree.ElementTree** | Geração do XML|
| **SQL** | Extração |

## Arquitetura do Pipeline

```
BigQuery (DW)
    │
    ├── SQL com CTEs
    │     ├── cliente_conta       → vínculo cliente-produto
    │     ├── cadastro3040        → dados cadastrais
    │     ├── total_cli           → contagem de clientes únicos por período
    │     ├── vencimentos3040     → distribuição por vértices de prazo
    │     └── operacao3040        → dados das operações de crédito
    │
    ▼
pandas DataFrame
    │
    ▼
Geração do XML
    └── Doc3040
          └── <Cli> por cliente
                └── <Op> por operação
                      ├── <Venc>               → vértices de prazo
                      ├── <Inf>                → tipo/produto
                      └── <ContInstFinRes4966> → risco de crédito (Res. 4966)
    │
    ▼
Arquivo CADOC3040_<CNPJ>_<DATA>.xml
```

## Como Executar
```bash
pip install pandas
jupyter notebook cadoc3040_portfolio.ipynb
```

### Com BigQuery
```bash
pip install google-cloud-bigquery pandas db-dtypes
```

Configure sua service account e substitua o bloco de dados fictícios pela autenticação real:
```python
from google.oauth2 import service_account
from google.cloud import bigquery

credentials = service_account.Credentials.from_service_account_file(
    "caminho/para/credentials.json",
    scopes=["https://www.googleapis.com/auth/cloud-platform"]
)
client = bigquery.Client(credentials=credentials, project=credentials.project_id)
df = client.query(QUERY_REFERENCIA).to_dataframe()
```

## Estrutura do Repositório

```
cadoc3040/
├── cadoc3040_portfolio.ipynb   # Pipeline principal
├── README.md                   # Este arquivo
└── output/                     # XMLs gerados
```

## 📄 Regulamentação

- [Resolução BCB nº 4.966/2021](https://www.bcb.gov.br) — Risco de Crédito
- [Manual CADOC 3040](https://www.bcb.gov.br) — Especificação do arquivo

---

> ⚠️ Os dados utilizados neste repositório são **completamente fictícios**, gerados para fins de demonstração técnica.
