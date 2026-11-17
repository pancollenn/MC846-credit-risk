# 📋 Planejamento — Projeto Final MC846
> **Aprendizado de Máquina Supervisionado**  
> Ciclo Completo de Modelagem Preditiva: Premissas Teóricas, Ensembles, Código Reprodutível e Avaliação  
> **Prazo de entrega:** 30 de novembro de 2026, às 23h59

---

## 🗂️ Visão Geral

**Domínio:** Risco de Crédito — Finanças  
**Dataset:** [Give Me Some Credit](https://www.kaggle.com/competitions/GiveMeSomeCredit) (Kaggle)  
**Pergunta de pesquisa:** Dado o perfil financeiro e histórico de inadimplência de um tomador de crédito, qual a probabilidade de ele apresentar **inadimplência grave (≥90 dias) nos próximos 2 anos**?

O projeto desenvolve e avalia um ciclo completo de modelagem preditiva supervisionada, conectando prática computacional ao rigor teórico da disciplina. O foco não é apenas maximizar métricas, mas conduzir uma **investigação analítica aprofundada** sobre premissas, mecanismos de erro e teoria de decisão aplicada ao contexto de crédito.

### 📊 Ficha do Dataset

| Atributo | Valor |
|----------|-------|
| Arquivo principal | `data/cs-training.csv` |
| N (observações) | **150.000** ✅ |
| d (features) | **10** ✅ |
| Variável alvo | `SeriousDlqin2yrs` (binária: 0/1) |
| Positivos (default) | **10.026** (6,68%) — desbalanceado |
| Negativos (adimplente) | **139.974** (93,32%) |

### 🔍 Features

| Feature | Tipo | Obs. |
|---------|------|------|
| `RevolvingUtilizationOfUnsecuredLines` | Contínua | Outliers extremos (máx: 50.708!) |
| `age` | Inteira | Anomalia: `age=0` presente |
| `NumberOfTime30-59DaysPastDueNotWorse` | Discreta | Outliers: valores 96/98 suspeitos |
| `DebtRatio` | Contínua | Outliers extremos (máx: 329.664!) |
| `MonthlyIncome` | Contínua | **19,82% missing** — principal desafio |
| `NumberOfOpenCreditLinesAndLoans` | Discreta | — |
| `NumberOfTimes90DaysLate` | Discreta | Outliers: valores 96/98 suspeitos |
| `NumberRealEstateLoansOrLines` | Discreta | — |
| `NumberOfTime60-89DaysPastDueNotWorse` | Discreta | Outliers: valores 96/98 suspeitos |
| `NumberOfDependents` | Discreta | **2,62% missing** |

### ⚠️ Desafios Identificados

- **Desbalanceamento de classes:** apenas 6,68% de positivos → métricas adequadas: **ROC-AUC** e **PR-AUC**
- **Missings substanciais:** `MonthlyIncome` (19,82%) exige estratégia de imputação justificada
- **Outliers extremos:** `DebtRatio` e `RevolvingUtilizationOfUnsecuredLines` com valores absurdos
- **Anomalias no alvo:** valores `96`/`98` em variáveis de atraso (provavelmente sentinelas)
- **Custo assimétrico natural:** falso negativo (conceder crédito a um inadimplente) tipicamente muito mais caro do que falso positivo (negar crédito a um bom pagador)
---

## 📦 Entregáveis

| # | Entregável | Descrição |
|---|------------|-----------|
| 1 | **Relatório Técnico (PDF)** | 10–15 páginas, estruturado em 6 seções |
| 2 | **Repositório de Código Reprodutível** | Gerenciado com `uv`, com `pyproject.toml`, `uv.lock`, `.python-version` e `README.md` |

---

## 📐 Critérios de Avaliação

| Dimensão | Peso |
|----------|------|
| Contextualização e Sanidade dos Dados | 15% |
| Formalismo Teórico e Hipóteses Prévias | 25% |
| Protocolo Experimental, Ensembles e Viés-Variância | 30% |
| Resultados, Decisão e Interpretabilidade | 15% |
| Reprodutibilidade e Código com `uv` | 15% |

---

## 🗓️ Fases do Projeto

### Fase 0 — Configuração do Ambiente e do Repositório ✅
**Meta:** Repositório funcional com ambiente `uv` configurado.

- [ ] Criar repositório Git limpo
- [x] Definir `pyproject.toml` com dependências (scikit-learn, pandas, numpy, matplotlib, xgboost, lightgbm, imbalanced-learn, scipy + grupo dev com jupyter, black, ruff)
- [x] Gerar `uv.lock` com `uv sync` — 123 pacotes resolvidos deterministicamente
- [x] Fixar versão do Python em `.python-version` → `3.12`
- [x] Criar estrutura de diretórios do projeto:
  ```
  .
  ├── src/
  │   ├── __init__.py
  │   ├── main.py       ← entrypoint: uv run python src/main.py
  │   ├── data.py
  │   ├── models.py
  │   ├── evaluate.py
  │   └── plots.py
  ├── notebooks/
  │   ├── 01_gate_sanidade.ipynb
  │   └── 02_pipeline_completo.ipynb
  ├── data/             ← não versionado no git
  ├── reports/          ← saídas geradas pelo pipeline
  ├── planejamento/
  ├── pyproject.toml
  ├── uv.lock
  ├── .python-version
  ├── .gitignore
  └── README.md
  ```
- [x] Redigir `README.md` com instruções de reprodução (`uv sync` + `uv run python src/main.py`)
- [x] Atualizar `.gitignore` (exclui `.venv/`, `data/*.csv`, `reports/`, caches Python/Jupyter)

---

### Fase 1 — Dataset e Gate de Sanidade ✅
**Meta:** Confirmar que o dataset atende todos os critérios de não-trivialidade.

> [!IMPORTANT]
> Dataset **Give Me Some Credit** selecionado. Verificação dos critérios abaixo:

**Critérios de não-trivialidade:**
- [x] N ≥ 1.000 observações → **N = 150.000** ✅
- [x] d ≥ 8 atributos informativos → **d = 10** ✅
- [x] Dataset não-trivial (não é Iris, Titanic, Boston Housing ou Wine Quality) ✅
- [ ] Baseline linear ingênuo com ROC-AUC ≤ 0,92 — **a verificar empiricamente**

**Tarefas:**
- [x] Selecionar domínio: **Risco de Crédito (Finanças)**
- [x] Baixar dataset em `data/` (`cs-training.csv`, `cs-test.csv`, `Data Dictionary.xls`)
- [ ] Executar EDA inicial e sanidade em notebook `notebooks/01_eda.ipynb`:
  - [x] Volume: N = 150.000 × d = 10 features
  - [x] Tipos de variáveis: todas numéricas (contínuas e discretas)
  - [x] Missings: `MonthlyIncome` (19,82%) e `NumberOfDependents` (2,62%)
  - [x] Desbalanceamento: 6,68% positivos (`SeriousDlqin2yrs = 1`)
  - [ ] Identificar e tratar outliers extremos (`DebtRatio`, `RevolvingUtilizationOfUnsecuredLines`, valores 96/98)
  - [ ] Tratar anomalia `age = 0`
- [ ] Treinar regressão logística ingênua (sem tuning) e confirmar ROC-AUC ≤ 0,92

---

### Fase 2 — Seção 1 do Relatório: Contextualização e Sanidade dos Dados
**Estimativa:** 1–2 páginas | **Peso:** 15%

- [ ] Redigir contexto: **predição de risco de crédito** — minimizar perdas por inadimplência em carteiras de crédito pessoal
- [ ] Formalizar matematicamente:
  - X ∈ ℝ^{150000×10} (perfil financeiro e histórico de pagamentos)
  - y ∈ {0, 1}^{150000} (inadimplência grave nos próximos 2 anos)
  - Função objetivo: minimizar perdas esperadas por inadimplência (Risco de Bayes com custos assimétricos)
- [ ] Descrever as 10 features e seus tipos estatísticos (todas numéricas)
- [ ] Documentar diagnóstico de sanidade:
  - Missings: `MonthlyIncome` (19,82%), `NumberOfDependents` (2,62%)
  - Outliers: `DebtRatio` máx=329.664, `RevolvingUtilizationOfUnsecuredLines` máx=50.708
  - Sentinelas suspeitas: valores 96/98 em variáveis de atraso
  - Anomalia: `age = 0`
  - Desbalanceamento: 6,68% positivos
- [ ] Justificar métricas de avaliação:
  - **Métrica primária: ROC-AUC** — robusto ao desbalanceamento, amplamente usado em scoring de crédito
  - **Métrica secundária: PR-AUC** — mais informativa quando a classe positiva é rara
  - Justificar que Acurácia seria enganosa (93% chutando sempre "adimplente")
- [ ] Implementar e reportar baseline ingênuo: **estimador majoritário** e **regressão logística sem tuning**

---

### Fase 3 — Seção 2 do Relatório: Formalismo Teórico e Hipóteses
**Estimativa:** 2 páginas | **Peso:** 25%

> [!NOTE]
> Esta é a seção mais importante conceitualmente. Selecionar **pelo menos 3 famílias distintas**.

**Famílias de modelos propostas para o problema de crédito:**

#### 🔵 1. Regressão Logística com Regularização (L1/L2)
- **Hipótese:** Y|X ~ Bernoulli(σ(βᵀX)) — log-odds de default é linear nas features
- **Regularização:** Ridge → priori Gaussiana em β (MAP); Lasso → priori de Laplace (esparsidade)
- **Adequação ao problema:** Features de crédito têm relação plausível com log-odds de default; `DebtRatio` e `RevolvingUtilizationOfUnsecuredLines` provavelmente têm efeito monotônico. Contudo, interações entre variáveis de atraso e renda podem ser não-lineares
- [ ] Documentar premissas, derivação do gradiente e interpretação dos coeficientes como log-odds ratio

#### 🟠 2. Árvore de Decisão CART
- **Hipótese:** Particionamento constante por partes do espaço de features; maximização gulosa de pureza (Gini) em cada nó
- **Adequação ao problema:** Captura naturalmente não-linearidades (ex.: risco dispara acima de um limiar de `NumberOfTimes90DaysLate`); sensível a outliers extremos nas features
- [ ] Documentar critério de divisão (Gini), poda por custo-complexidade, instabilidade de alta variância

#### 🟢 3. Gradient Boosting (XGBoost ou LightGBM)
- **Hipótese:** Soma de árvores rasas aprendidas sequencialmente por descida de gradiente funcional no espaço de funções
- **Adequação ao problema:** Redução de viés iterativa; lida bem com outliers e missings (LightGBM tem suporte nativo); muito bem calibrado para scoring de crédito em competições Kaggle
- [ ] Documentar mecanismo de boosting, perda logística, shrinkage e regularização

#### (Opcional) 🔴 4. Random Forest
- **Hipótese:** Bagging de árvores profundas com amostragem aleatória de features (descorrelação)
- **Adequação ao problema:** Reduz variância das árvores instáveis; robusto a outliers moderados
- [ ] Documentar bootstrap, amostragem de atributos e mecanismo de redução de variância

**Hipótese prévia (pré-experimental):**
- [ ] **Hipótese de decomposição de erro:** O problema provavelmente é dominado por **viés** nas representações lineares — as fronteiras de decisão são fortemente não-lineares (e.g., interação entre histórico de atrasos múltiplos e renda). Árvores individuais sofrem de alta variância dado o ruído nas features
- [ ] **Previsão:** **Gradient Boosting** deve superar todos os modelos individuais e possivelmente o Random Forest, por reduzir tanto viés quanto variância iterativamente
- [ ] **Critério de falseamento:** Se a Regressão Logística Regularizada atingir ROC-AUC superior ao Gradient Boosting no conjunto de teste, isso indicaria que a fronteira de Bayes é essencialmente plana/linear para estes atributos — refutando a hipótese de dominância por viés

---

### Fase 4 — Seção 3 do Relatório: Protocolo Experimental
**Estimativa:** 1 página | **Peso:** parte do 30%

- [ ] **Particionamento:** Treino (70%) / Validação (15%) / Teste (15%) com **estratificação** pela variável alvo (preservar proporção 6,68% positivos em cada split)
  - Justificativa: Estratificação obrigatória dado o desbalanceamento — splits aleatórios sem estratificação podem gerar partições com proporções muito distintas de positivos
  - Sem particionamento temporal (dados não possuem ordem temporal explícita no arquivo)
- [ ] **Ausência de data leakage:**
  - Imputação de `MonthlyIncome` e `NumberOfDependents` fitada APENAS no treino (mediana ou modelo de imputação)
  - Escalonamento (StandardScaler ou RobustScaler para outliers) fitado APENAS no treino
  - Tratamento de outliers e sentinelas definido por regras fixas antes do split (ou fitado no treino)
  - `cs-test.csv` do Kaggle **não utilizado como conjunto de teste** (sem labels) — usar split do `cs-training.csv`
- [ ] **Estratégia de imputação a documentar:**
  - `MonthlyIncome` (19,82%): imputação por mediana do treino ou por modelo (KNNImputer) — justificar a escolha
  - `NumberOfDependents` (2,62%): imputação por mediana (0)
- [ ] **Pipeline scikit-learn:** implementar com `Pipeline` + `ColumnTransformer` para garantir ausência de leakage
- [ ] **Otimização de hiperparâmetros:** `RandomizedSearchCV` com 5-fold estratificado (StratifiedKFold) no conjunto de treino+validação
  - Hiperparâmetros-chave por modelo: C e penalty (Logística), max_depth e min_samples_leaf (CART), n_estimators / max_depth / learning_rate / subsample (Gradient Boosting)

---

### Fase 5 — Seção 4: Modelagem, Ensembles e Decomposição de Erro
**Estimativa:** 3–4 páginas | **Peso:** 30%

#### 5a — Modelos Base Individuais
- [ ] Treinar **Regressão Logística** (L1/L2) com `RobustScaler` (robusto a outliers de `DebtRatio`, etc.)
- [ ] Treinar **Árvore de Decisão CART** com poda via `ccp_alpha`
- [ ] Treinar **Gradient Boosting** (XGBoost ou LightGBM — suporte nativo a missings é vantagem)
- [ ] Plotar curvas de aprendizado e validação para cada modelo
- [ ] Diagnosticar overfitting vs. underfitting:
  - Árvore profunda: overfitting esperado → validar com curva de profundidade vs. ROC-AUC
  - Regressão Logística: underfitting esperado se a fronteira for não-linear
- [ ] Registrar ROC-AUC e PR-AUC de cada modelo base na validação

#### 5b — Ensembles (Obrigatório)
- [ ] Implementar **Random Forest** (Bagging de árvores com amostragem de features)
- [ ] Implementar **Gradient Boosting** (XGBoost ou LightGBM) com tuning de hiperparâmetros
- [ ] Comparar ROC-AUC dos ensembles vs. modelos base individuais

#### 5c — Análise Mecanística (Viés-Variância) — aplicada ao crédito
- [ ] Responder: o ensemble melhorou? Conectar ao mecanismo teórico:
  - **Random Forest** vs. CART individual: a árvore individual sofria de alta variância? O bootstrap + amostragem de features reduziu a correlação entre estimadores e estabilizou a previsão?
  - **Gradient Boosting** vs. Logística: o boosting conseguiu capturar as não-linearidades (interação atraso × renda) que a logística não conseguia? Houve redução de viés?
  - Verificar se há risco de **amplificação de ruído** no boosting dado o desbalanceamento (6,68% positivos)
- [ ] Análise de sensibilidade a hiperparâmetros-chave:
  - `n_estimators` M: curva de ROC-AUC em função do número de árvores
  - `max_depth`: efeito na estabilidade (RF) vs. capacidade (GB)
  - `learning_rate` η: trade-off com `n_estimators` no Gradient Boosting
- [ ] Confrontar com hipótese prévia: o Gradient Boosting superou o Random Forest conforme previsto?

---

### Fase 6 — Seção 5: Resultados, Calibração e Interpretabilidade
**Estimativa:** 2–3 páginas | **Peso:** 15%

#### 6a — Avaliação Final no Conjunto de Teste
- [ ] Gerar tabela consolidada de todos os modelos (Logística, CART, RF, GB) no conjunto de teste
- [ ] Plotar curvas ROC e Precision-Recall para todos os modelos
- [ ] Calcular intervalos de incerteza via Bootstrap (1.000 reamostras) para ROC-AUC do modelo campeão

#### 6b — Calibração de Probabilidades — crítico para scoring de crédito
- [ ] Inspecionar calibração do modelo campeão via **reliability diagram** (curva de calibração)
  - Gradient Boosting e Random Forest tendem a ser mal calibrados em extremos
- [ ] Aplicar **calibração a posteriori** se necessário: Platt scaling (`CalibratedClassifierCV(method='sigmoid')`) ou regressão isotônica
- [ ] **Ajuste de limiar via Matriz de Perdas** — contexto de crédito:
  - Definir custos reais: Falso Negativo (conceder crédito a inadimplente) >> Falso Positivo (negar crédito a bom pagador)
  - Ex.: custo FN = 10x custo FP (perda de capital vs. perda de receita de juros)
  - Demonstrar minimização do Risco de Bayes: limiar ótimo = custo_FP / (custo_FP + custo_FN)
  - Comparar métricas com limiar ingênuo 0,5 vs. limiar ótimo

#### 6c — Interpretabilidade Pós-Ajuste — aplicada ao crédito
- [ ] **MDI** vs. **MDA** do modelo campeão:
  - Verificar se `NumberOfTimes90DaysLate` e `RevolvingUtilizationOfUnsecuredLines` dominam (esperado)
  - Checar se MDI superestima features com alta cardinalidade ou missings (`MonthlyIncome`)
  - MDA deve confirmar quais features realmente degradam o erro quando permutadas
- [ ] **PDPs** para os 2 atributos mais influentes:
  - Ex.: PDP de `NumberOfTimes90DaysLate`: espera-se relação monotônica crescente com risco
  - Ex.: PDP de `RevolvingUtilizationOfUnsecuredLines`: não-linearidade esperada (risco dispara acima de ~90%)
- [ ] **Contraste com coeficientes β da Regressão Logística:**
  - Os mesmos atributos são destacados? Discrepâncias revelam interações não-lineares capturadas pelo ensemble?

#### 6d — Trade-offs Práticos
- [ ] Discutir: o ganho de ROC-AUC do GB/RF justifica a menor interpretabilidade frente à Logística?
- [ ] Em regulação de crédito (ex.: LGPD / Fair Lending), modelos precisam ser explicáveis — SHAP como ponte?
- [ ] Estimar custo computacional: tempo de treino e latência de inferência por transação

---

### Fase 7 — Seção 6: Conclusão e Diagnóstico Crítico
**Estimativa:** 1 página

- [ ] Declarar modelo vencedor e justificar
- [ ] Confrontar resultado empírico com hipótese inicial (confirmada ou refutada?)
- [ ] O que aprendemos com eventuais discrepâncias?
- [ ] Levantar limitações do dataset e variáveis confundidoras não observadas
- [ ] Propor trabalhos futuros concretos

---

### Fase 8 — Revisão Final e Submissão
**Prazo:** até **30/11/2026 às 23h59**

- [ ] Revisar relatório completo (10–15 páginas, 6 seções)
- [ ] Garantir que todos os seeds/`random_state` estão fixados no código
- [ ] Testar reprodutibilidade do zero:
  ```bash
  uv sync
  uv run python src/main.py
  ```
- [ ] Verificar que figuras e tabelas do relatório batem exatamente com o código
- [ ] Exportar relatório como PDF
- [ ] Submeter relatório PDF + link do repositório

---

## ⚠️ Checklist de Pontos Críticos

> [!CAUTION]
> Falhas nestes pontos resultam em penalização direta nas rubricas.

- [ ] Dataset passa no "gate de sanidade" (N ≥ 1.000, d ≥ 8, baseline ≤ 0,92)
- [ ] **Nenhum** data leakage nos pipelines de pré-processamento
- [ ] Ensembles obrigatoriamente implementados e analisados mecanisticamente
- [ ] Hipótese de viés vs. variância registrada **antes** dos experimentos e confrontada depois
- [ ] Código 100% reprodutível via `uv sync && uv run python src/main.py`
- [ ] Seeds fixados em todos os pontos aleatórios

---

## 🔗 Referências Rápidas

- [Especificação do Projeto](./projeto.txt)
- [Dataset Give Me Some Credit — Kaggle](https://www.kaggle.com/competitions/GiveMeSomeCredit)
- [Data Dictionary](../data/Data%20Dictionary.xls)
- Gerenciador de ambiente: [uv](https://docs.astral.sh/uv/)
