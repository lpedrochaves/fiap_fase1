# NPS Preditivo — FIAP Tech Challenge (Fase 1)

Análise de satisfação de clientes de um e-commerce a partir de dados operacionais (pedido, logística e atendimento), com o objetivo de identificar os fatores associados ao `nps_score` e de propor um modelo capaz de sinalizar o risco de insatisfação **antes** da pesquisa de NPS ser aplicada.

## Objetivo

- Entender o problema de negócio por trás da variação do NPS entre clientes.
- Explorar a base histórica de pedidos, entregas e atendimento (EDA com foco em negócio).
- Identificar os fatores operacionais mais associados a detratores, e o "ponto de ruptura" da experiência.
- Propor e avaliar um modelo preditivo (regressão + classificação) para antecipar risco de insatisfação.
- Traduzir os achados em recomendações práticas para logística, atendimento e CX.

## Base de dados

O CSV original já está incluído em `data/raw/desafio_nps_fase_1.csv` (2.500 clientes / 2.500 pedidos) — é uma base sintética fornecida pelo curso, sem dados pessoais reais, por isso é versionada normalmente. O dicionário completo de colunas está em [`references/dicionario_dados.md`](references/dicionario_dados.md).

## Estrutura

```text
data/
├── raw/          # CSV original, imutável (versionado)
└── processed/    # saídas intermediárias eventuais (gitignored, regeneráveis pelos notebooks)
notebooks/         # análise, na ordem em que devem ser lidas/executadas
├── primeira_etapa.ipynb   # entendimento de negócio (conceitual)
├── segunda_etapa.ipynb    # definição da variável-alvo (conceitual)
├── terceira_etapa.ipynb   # EDA com foco em negócio
└── quarta_etapa.ipynb     # modelo preditivo (regressão + classificação) — etapa opcional
models/            # artefatos de modelo treinado, se exportados (gitignored)
reports/           # apresentação executiva para stakeholders
└── apresentacao_executiva.html
references/        # dicionário de dados e notas de apoio
```

## Metodologia

1. **Entendimento de negócio e da variável-alvo** (`primeira_etapa.ipynb`, `segunda_etapa.ipynb`): por que o NPS importa, quem se beneficia dos insights, e por que `nps_score` é a variável-alvo — incluindo os riscos de usá-la de forma inadequada.
2. **EDA** (`terceira_etapa.ipynb`): qualidade da base, distribuição do NPS, comparação entre promotores e detratores, segmentação por atraso de entrega / contatos de atendimento / reclamações / região, e correlação de cada variável com o NPS.
3. **Modelo preditivo — opcional** (`quarta_etapa.ipynb`):
   - Regressão (`RandomForestRegressor`) para estimar a nota contínua de NPS.
   - Classificação por categoria (Detrator/Passivo/Promotor) e classificação binária de risco de detrator (`RandomForestClassifier` / `LogisticRegression`).
   - Split treino/teste 80/20 estratificado; features que só existem *depois* da jornada do cliente (`repeat_purchase_30d`, `csat_internal_score`) são explicitamente excluídas para evitar vazamento de informação — ver [`references/dicionario_dados.md`](references/dicionario_dados.md#risco-de-vazamento-data-leakage).
   - Importância de variáveis via *permutation importance*.

### Principais resultados

- NPS agregado da amostra: **−80** (84,4% detratores, 11,2% passivos, 4,4% promotores).
- Maiores associações com NPS baixo: `delivery_delay_days` (correlação −0,60), `complaints_count` (−0,50), `customer_service_contacts` (−0,35).
- Regressão do NPS: MAE 1,33 / R² 0,54.
- Classificação binária de risco de detrator: acurácia 0,80, precisão 0,92 e recall 0,79 na classe detrator.

Detalhes, gráficos e a leitura de negócio completa estão em `notebooks/terceira_etapa.ipynb`, `notebooks/quarta_etapa.ipynb` e em [`reports/apresentacao_executiva.html`](reports/apresentacao_executiva.html).

## Como reproduzir

No Windows (PowerShell):

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
jupyter lab
```

Depois, abra os notebooks em `notebooks/` na ordem indicada acima (`primeira_etapa` → `quarta_etapa`) e execute célula a célula. Todos leem diretamente de `data/raw/desafio_nps_fase_1.csv`, então nenhum passo de setup adicional é necessário.

Para reexecutar um notebook de ponta a ponta pela linha de comando (útil para checar reprodutibilidade sem abrir o Jupyter):

```powershell
python -m jupyter nbconvert --to notebook --execute --inplace notebooks/quarta_etapa.ipynb
```

## Entregáveis do Tech Challenge

- [x] Entendimento do negócio (`notebooks/primeira_etapa.ipynb`)
- [x] Definição da target (`notebooks/segunda_etapa.ipynb`)
- [x] EDA com foco em negócio (`notebooks/terceira_etapa.ipynb`)
- [x] Modelo preditivo opcional, com pipeline completo (`notebooks/quarta_etapa.ipynb`)
- [x] Apresentação executiva (`reports/apresentacao_executiva.html`)
- [ ] Vídeo executivo (até 5 minutos)
