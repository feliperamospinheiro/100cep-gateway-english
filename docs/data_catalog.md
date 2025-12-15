# Catálogo de Dados — 100cep Gateway
---

# 🥇 Gold

| Coluna | Tipo | Descrição |
| ------ | ---- | --------- |
| chargeback_id | Descrição |
| motivo_chargeback | Descrição |
| status_chargeback | Descrição |
| resposta_emitente | Descrição |
| resposta_adquirente | Descrição |

### dim_clientes
| Coluna | Tipo | Descrição |
| ------ | ---- | --------- |
| cliente_id | String | Unique identifier for each client, consisting of 13 alphanumeric characters in lowercase. / Identificador único para cada cliente, composto por 13 caracteres alfanuméricos em minúsculas. |
| cep_prefixo | Tipo | Descrição |


### dim_data
| Coluna | Tipo | Descrição |
| ------ | ---- | --------- |
| data_calendario | Tipo | Descrição |
| dia | Tipo | Descrição |
| mes | Tipo | Descrição |
| ano | Tipo | Descrição |
| nome_dia_semana | Tipo | Descrição |
| nome_mes | Tipo | Descrição |

### dim_geolocalizacao
| Coluna | Tipo | Descrição |
| ------ | ---- | --------- |
| cep_prefixo | Tipo | Descrição |
| cidade | Tipo | Descrição |
| estado | Tipo | Descrição |
| latitude | Tipo | Descrição |
| longitude | Tipo | Descrição |

### dim_pagamentos
| Coluna | Tipo | Descrição |
| ------ | ---- | --------- |
| id_pagamento | Tipo | Descrição |
| tipo_pagamento | Tipo | Descrição |
| nivel_risco | Tipo | Descrição |

### dim_vendedores
| Coluna | Tipo | Descrição |
| ------ | ---- | --------- |
| vendedor_id | String | Unique identifier for each seller, consisting of 13 alphanumeric characters in lowercase. / Identificador único para cada vendedor, composto por 13 caracteres alfanuméricos em minúsculas. |
| cep_prefixo | Tipo | Descrição |

### fato_transacoes
| Coluna | Tipo | Descrição |
| ------ | ---- | --------- |
| pedido_id | String | Unique identifier for each transaction, consisting of 13 alphanumeric characters in lowercase. / Identificador único para cada transação, composto por 13 caracteres alfanuméricos em minúsculas. |
| cliente_id | String | Unique identifier for each client, consisting of 13 alphanumeric characters in lowercase. / Identificador único para cada cliente, composto por 13 caracteres alfanuméricos em minúsculas. |
| vendedor_id | String | Unique identifier for each seller, consisting of 13 alphanumeric characters in lowercase. / Identificador único para cada vendedor, composto por 13 caracteres alfanuméricos em minúsculas. |

---

# Linhagem de Dados
- Kaggle → Bronze → Silver → Gold  
- Transformações documentadas em `/docs/etl_documentation.md`.  