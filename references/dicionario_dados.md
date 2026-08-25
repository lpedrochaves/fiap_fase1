# Dicionário de dados

Fonte: `data/raw/desafio_nps_fase_1.csv` (2.500 clientes / 2.500 pedidos), conforme enunciado do Tech Challenge Fase 1 (FIAP).

| Coluna | Descrição |
|---|---|
| `customer_id` | Identificador único do cliente. |
| `order_id` | Identificador único do pedido. |
| `customer_age` | Idade do cliente. |
| `customer_region` | Região geográfica do cliente. |
| `customer_tenure_months` | Tempo de relacionamento do cliente com a empresa (em meses). |
| `order_value` | Valor total do pedido. |
| `items_quantity` | Quantidade de itens no pedido. |
| `discount_value` | Valor de desconto aplicado ao pedido. |
| `payment_installments` | Número de parcelas do pagamento. |
| `delivery_time_days` | Tempo total de entrega (em dias). |
| `delivery_delay_days` | Quantidade de dias de atraso na entrega. |
| `freight_value` | Valor do frete. |
| `delivery_attempts` | Número de tentativas de entrega. |
| `customer_service_contacts` | Número de contatos do cliente com o atendimento. |
| `resolution_time_days` | Tempo para resolução de problemas (em dias). |
| `complaints_count` | Número de reclamações registradas pelo cliente. |
| `repeat_purchase_30d` | Indica se houve recompra em até 30 dias após o pedido (0 = não, 1 = sim). |
| `csat_internal_score` | Score interno de satisfação do cliente. |
| `nps_score` | **Variável-alvo.** Nota de satisfação do cliente (NPS), de 0 a 10, coletada após a experiência de compra. |

## Variáveis derivadas usadas nos notebooks

| Coluna | Definição | Onde é criada |
|---|---|---|
| `categoria_nps` / `nps_category` | `Detrator` (0–6) / `Passivo` (7–8) / `Promotor` (9–10), a partir de `nps_score`. | `notebooks/terceira_etapa.ipynb`, `notebooks/quarta_etapa.ipynb` |
| `is_detrator` | Indicador binário: 1 se `nps_score` ≤ 6. | `notebooks/quarta_etapa.ipynb` |

## Risco de vazamento (data leakage)

`repeat_purchase_30d` e `csat_internal_score` só são conhecidos **depois** da jornada do cliente (a recompra acontece depois, e o CSAT interno tende a ser coletado junto ou após o NPS). Por isso são **excluídos das features** de qualquer modelo que tente prever o NPS antes da pesquisa — ver `VARIAVEIS_COM_VAZAMENTO` em `notebooks/quarta_etapa.ipynb`.

Da mesma forma, `categoria_nps` e `is_detrator` são derivadas diretamente de `nps_score` e nunca devem ser usadas como *feature* de um modelo cujo alvo também derive de `nps_score` — usar uma para prever a outra vaza a própria resposta.
