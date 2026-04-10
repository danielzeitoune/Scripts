# DRSAC — Pipeline de Geração XML Regulatório

Pipeline em Python para geração automática do **DRSAC (Documento de Risco Socioambiental e Climático)**, relatório obrigatório exigido pelo Banco Central do Brasil (BACEN) para instituições financeiras.

## Sobre o Projeto

O **DRSAC** é uma obrigação regulatória que exige que instituições financeiras avaliem e reportem os riscos socioambientais e climáticos de suas carteiras de crédito. Este pipeline automatiza a geração do arquivo XML a partir de uma planilha de dados.

## Stack Utilizada

| Tecnologia | Uso |
|---|---|
| **Python 3.12** | Linguagem principal |
| **pandas** | Leitura da planilha e transformação dos dados |
| **openpyxl** | Engine de leitura de arquivos `.xlsx` |
| **xml.etree.ElementTree** | Geração do XML

## Arquitetura do Pipeline

```
Planilha Excel (.xlsx)
    │
    ▼
pandas DataFrame
    │  (uma linha = uma operação de crédito)
    ▼
Agrupamento por cliente (clientes_dict)
    │
    ▼
Geração do XML hierárquico
    └── DocumentoDRSAC
          ├── Contato
          └── Clientes
                └── Cliente (por CNPJ/CPF)
                      ├── ExpAtivos
                      │     └── ExpOperCred (por operação)
                      │           ├── RiscSoc       (6 tipos)
                      │           ├── RiscAmb       (10 tipos)
                      │           ├── RiscClimFis   (4 tipos)
                      │           ├── RiscClimTrans (5 tipos)
                      │           ├── ContribPositiva
                      │           ├── MitRiscClimFis
                      │           ├── HistAbsorEmissGEE
                      │           ├── CompEmissGEE
                      │           └── LocalizCEP
                      └── ExpCliente
                            └── (mesma estrutura de risco + GEE + AgrMit)
    │
    ▼
Arquivo <CodDoc>_<Ano>_<Mes>_<Remessa>.xml
```

## Como Executar

### Com dados fictícios (demonstração)
```bash
pip install pandas openpyxl
jupyter notebook drsac_portfolio.ipynb
```

### Com planilha real
Substitua o bloco de dados fictícios pela leitura do arquivo:
```python
df = pd.read_excel("caminho/para/Dados_DRSAC.xlsx", engine="openpyxl")
```

## Estrutura do Repositório

```
drsac/
├── drsac_portfolio.ipynb   # Pipeline principal
├── README.md               # Este arquivo
└── output/                 # XMLs gerados (criado automaticamente)
```

## Colunas Esperadas na Planilha

| Coluna | Descrição |
|---|---|
| `cnpj` | CNPJ do cliente |
| `dataBase` | Competência (YYYY-MM) |
| `codigoDocumento` | Código do documento BACEN |
| `tipoEnvio` | Tipo de envio (`N` = normal) |
| `nome`, `fone`, `email` | Responsável pelo envio |
| `ident` | Identificador único do cliente |
| `CNAE`, `versaoCNAE` | Classificação de atividade |
| `IPOC`, `Sicor` | Identificadores da operação |
| `saldo` | Saldo da operação (R$) |
| `CEP` | CEP da operação |
| `RiscSoc_01` .. `RiscSoc_99` | Avaliação risco social |
| `RiscAmb_01` .. `RiscAmb_99` | Avaliação risco ambiental |
| `RiscClimFis_01` .. `RiscClimFis_99` | Risco climático físico |
| `RiscClimTrans_01` .. `RiscClimTrans_99` | Risco climático de transição |

## Observações de Segurança
- Nunca versione planilhas com dados reais de clientes
- Adicione ao `.gitignore`: `*.xlsx`, `*.xls`, `output/`, `.env`
- Dados de contato do responsável devem ser carregados via variáveis de ambiente em produção

## Regulamentação

- [Resolução CMN nº 4.943/2021](https://www.bcb.gov.br) — Política de Responsabilidade Socioambiental e Climática
- [Manual DRSAC (BACEN)](https://www.bcb.gov.br) — Especificação do documento

---

> Os dados utilizados neste repositório são **completamente fictícios**, gerados para fins de demonstração técnica.
