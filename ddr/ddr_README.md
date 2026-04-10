# DDR 2011 — Pipeline de Geração XML Regulatório

Pipeline em Python para geração automática do **DDR 2011 (Demonstrativo de Depósitos e Recursos no Exterior)**, relatório diário exigido pelo Banco Central do Brasil (BACEN).

## Sobre o Projeto

O DDR 2011 exige que instituições financeiras reportem diariamente posições em depósitos no exterior, fatores de atualização e CVA. O pipeline automatiza a coleta dos dados, a atualização da planilha de controle e a geração do arquivo XML.

## Fluxo do Pipeline

```
1. cotacao_dolar.py
   └── Consulta API PTAX (BACEN) → grava cotacao USD na planilha

2. multip_13804.py
   └── Scraping do SGS (BACEN) → grava multiplicador (serie 13804) na planilha

3. ddr_xml.ipynb
   └── Le planilha atualizada → extrai ultima data-base valida → gera XML → compacta em ZIP
```

## Stack Utilizada

| Tecnologia | Uso |
|---|---|
| **Python 3.12** | Linguagem principal |
| **pandas / openpyxl** | Leitura e escrita de planilhas Excel |
| **requests** | Consumo da API PTAX do BACEN |
| **Selenium** | Web scraping do SGS (BACEN) |
| **lxml** | Geração do XML regulatório |
| **zipfile** | Compactação do arquivo para envio |

## Estrutura do XML Gerado

```
documentoDDR
  ├── parametros
  │     ├── parametro (31 — nome responsável)
  │     ├── parametro (32 — telefone)
  │     └── parametro (33 — email)
  └── contas
        ├── conta 111000 (Deposito NY — com detalhamento de moeda)
        ├── conta 310105 (Fator F)
        ├── conta 410100 (Fator Incorp.)
        ├── conta 410101 (Multiplicador M)
        └── conta 411000 (CVA)
```

## Como Executar

```bash
pip install pandas openpyxl requests selenium webdriver-manager lxml
```

Configure os caminhos e dados do responsável nas constantes no topo de cada arquivo, então execute na ordem:

```bash
python cotacao_dolar.py
python multip_13804.py
jupyter notebook ddr_xml.ipynb
```

## Estrutura do Repositório

```
ddr/
├── cotacao_dolar.py   # Coleta cotacao USD via API PTAX
├── multip_13804.py    # Coleta multiplicador via scraping SGS
├── ddr_xml.ipynb      # Gera XML e ZIP final
├── README.md          # Este arquivo
└── output/            # Arquivos gerados (criado automaticamente)
```

## Observacoes de Segurança

- Nunca versione a planilha de dados reais
- Adicione ao `.gitignore`: `*.xlsx`, `*.xls`, `output/`, `.env`
- Dados do responsável pelo envio devem ser carregados via variáveis de ambiente em produção

## Regulamentação

- [Circular BCB nº 3.691/2013](https://www.bcb.gov.br) — DDR
- [API PTAX — BACEN](https://olinda.bcb.gov.br/olinda/servico/PTAX/versao/v1/swagger-ui3)
- [SGS — Sistema Gerenciador de Series Temporais](https://www3.bcb.gov.br/sgspub)

---

> Os dados utilizados neste repositório são completamente fictícios, gerados para fins de demonstração técnica.
