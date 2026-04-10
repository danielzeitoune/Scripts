# Curvas B3 — Coleta e Interpolação de Taxas Referenciais

Pipeline em Python para coleta automática das curvas de juros do portal da B3 e aplicação de interpolação cúbica spline para geração de taxas em dias intermediários.

## Sobre o Projeto

As taxas referenciais divulgadas pela B3 (PRE x DI, DI x IPCA, DI x IGPM) cobrem apenas os vértices negociados no mercado de derivativos. Este pipeline coleta os dados do último dia útil do mês, grava na planilha de trabalho e interpola as taxas para todos os dias corridos do horizonte de interesse via spline cúbica natural.

## Fluxo

```
1. Calcula o ultimo dia util do mes anterior
   └── Considera feriados nacionais + fins de semana

2. Coleta as tres curvas via Selenium (portal B3)
   ├── PRE x DI
   ├── DI x IPCA
   └── DI x IGPM

3. Grava os dados brutos na planilha (aba Interpolacao)

4. Le os prazos e taxas coletados
   └── Aplica cubic spline natural nas tres curvas
   └── Salva os resultados interpolados na planilha
```

## Stack Utilizada

| Tecnologia | Uso |
|---|---|
| **Python 3.12** | Linguagem principal |
| **Selenium + webdriver-manager** | Web scraping do portal B3 |
| **pandas / openpyxl** | Leitura e escrita de planilhas |
| **Algoritmo próprio** | Interpolação cúbica spline natural |

## Como Executar

```bash
pip install pandas openpyxl selenium webdriver-manager urllib3
```

Configure os caminhos no topo do notebook:
```python
CAMINHO_FERIADOS = "caminho/para/feriados.xlsx"
CAMINHO_PLANILHA = "caminho/para/Curvas_B3.xlsx"
ABA_INTERPOLACAO = "Interpolacao"
```

Execute as células em ordem. O Chrome abrirá automaticamente para cada curva coletada.

## Estrutura do Repositório

```
curvas_b3/
├── curvas_b3.ipynb   # Pipeline principal
└── README.md         # Este arquivo
```

## Colunas Esperadas na Planilha

| Coluna | Curva |
|---|---|
| B, C, D | PRE x DI (dados brutos) |
| F | PRE x DI (interpolado) |
| J, K | DI x IPCA (dados brutos) |
| M | DI x IPCA (interpolado) |
| Q, R | DI x IGPM (dados brutos) |
| T | DI x IGPM (interpolado) |

## Observacoes de Segurança

- Nunca versione a planilha com dados reais
- Adicione ao `.gitignore`: `*.xlsx`, `*.xls`

## Fonte dos Dados

- [Taxas Referenciais BM&FBovespa — B3](https://www.b3.com.br/pt_br/market-data-e-indices/servicos-de-dados/market-data/consultas/mercado-de-derivativos/precos-referenciais/taxas-referenciais-bm-fbovespa/)
