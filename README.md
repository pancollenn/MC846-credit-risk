# MC846 — Predição de Risco de Crédito

> **Projeto Final — Aprendizado de Máquina Supervisionado (MC846)**  
> Dataset: [Give Me Some Credit](https://www.kaggle.com/competitions/GiveMeSomeCredit) (Kaggle)  
> **Pergunta de pesquisa:** Dado o perfil financeiro e histórico de inadimplência de um tomador de crédito, qual a probabilidade de ele apresentar inadimplência grave (≥90 dias) nos próximos 2 anos?

---

## Estrutura do Repositório

```
.
├── data/                        # Dados brutos (não versionados no git)
│   ├── cs-training.csv          # Conjunto de treino com labels (150.000 obs.)
│   ├── cs-test.csv              # Conjunto de teste Kaggle (sem labels)
│   └── Data Dictionary.xls      # Dicionário de variáveis
├── notebooks/
│   ├── 01_gate_sanidade.ipynb   # EDA e checagem de sanidade do dataset
│   └── 02_pipeline_completo.ipynb
├── src/
│   ├── __init__.py
│   ├── main.py                  # Ponto de entrada — executa o pipeline completo
│   ├── data.py                  # Carregamento e pré-processamento
│   ├── models.py                # Definição dos modelos e pipelines
│   ├── evaluate.py              # Métricas, curvas e calibração
│   └── plots.py                 # Visualizações (curvas ROC, PDP, importâncias)
├── reports/                     # Figuras e tabelas geradas (saída do pipeline)
├── planejamento/
│   ├── planejamento.md          # Planejamento detalhado do projeto
│   └── projeto.txt              # Especificação original do projeto
├── pyproject.toml               # Metadados e dependências (PEP 621)
├── uv.lock                      # Lockfile determinístico (gerado por uv)
├── .python-version              # Versão do Python fixada (3.12)
└── README.md
```

---

## Reprodução do Ambiente

Este projeto utiliza [**uv**](https://docs.astral.sh/uv/) para gerenciamento de dependências e ambiente virtual, garantindo reprodutibilidade exata via lockfile determinístico.

### Pré-requisitos

- [`uv`](https://docs.astral.sh/uv/getting-started/installation/) instalado (`curl -LsSf https://astral.sh/uv/install.sh | sh`)

### Passos

```bash
# 1. Clonar o repositório
git clone <url-do-repositorio>
cd mc846-credit-risk

# 2. Sincronizar o ambiente com o lockfile determinístico
uv sync

# 3. Executar o pipeline principal de modelagem
uv run python src/main.py

# 4. (Opcional) Abrir os notebooks de análise
uv run --with jupyter jupyter lab
```

> **Nota:** Os arquivos de dados (`data/`) não estão versionados no git por serem grandes.  
> Baixe `cs-training.csv` e `cs-test.csv` em [kaggle.com/competitions/GiveMeSomeCredit](https://www.kaggle.com/competitions/GiveMeSomeCredit) e coloque na pasta `data/`.

---

## Pipeline Principal (`src/main.py`)

O pipeline executa, em ordem, as seguintes etapas com **seeds fixadas** (`random_state=42`):

1. **Carregamento e sanidade** dos dados (`cs-training.csv`)
2. **Pré-processamento:** imputação de missings, tratamento de outliers, escalonamento robusto
3. **Split estratificado** treino/validação/teste (70/15/15)
4. **Treinamento** de modelos base: Regressão Logística (L1/L2), CART, Random Forest, Gradient Boosting
5. **Otimização de hiperparâmetros** via `RandomizedSearchCV` (5-fold estratificado)
6. **Avaliação** no conjunto de teste: ROC-AUC, PR-AUC, curvas, calibração
7. **Interpretabilidade:** MDI vs. MDA, PDPs dos atributos mais importantes
8. **Exportação** de figuras e tabelas para `reports/`

Todos os resultados, figuras e tabelas do relatório são 100% reproduzíveis a partir deste pipeline.

---

## Dependências Principais

| Pacote | Uso |
|--------|-----|
| `scikit-learn` | Modelos, pipelines, métricas, validação cruzada |
| `xgboost` / `lightgbm` | Gradient Boosting com suporte nativo a missings |
| `imbalanced-learn` | Estratégias para classes desbalanceadas |
| `pandas` / `numpy` | Manipulação de dados |
| `matplotlib` / `seaborn` | Visualizações |

---

## Equipe

- Projeto Final — MC846, Unicamp, 2026
