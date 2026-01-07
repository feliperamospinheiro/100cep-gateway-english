<h1 align="center">Documentação do ETL</h1>

Este documento descreve o processo de ETL do projeto **100cep Gateway**, estruturado na arquitetura de camadas **Bronze, Silver e Gold** sobre o Unity Catalog do Databricks, utilizando o dataset público de e‑commerce brasileiro da Olist e um dataset complementar de chargebacks.

***

## Visão geral do ambiente

O pipeline segue o padrão de arquitetura *medallion*, com dados organizados em camadas progressivas de refinamento (**Bronze → Silver → Gold**) para melhorar qualidade, governança e usabilidade analítica.
A gestão de dados, permissões e linhagem é feita via Unity Catalog, usando a convenção `catalog.schema.table` e separando schemas por camada (`staging`, `bronze`, `silver` e `gold`).

### Catálogo e schemas

- Catálogo: `100cep_gateway`.
- Schemas:
    - `staging`: recepção inicial de arquivos e volumes.
    - `bronze`: tabelas *raw* em formato Delta.
    - `silver`: dados limpos, padronizados e integrados.
    - `gold`: modelo analítico (fato e dimensões) pronto para consumo.

***

## Preparação do ambiente: [01_preparacao](.databricks/pipeline/notebooks/01_preparacao.ipynb)

O script de preparação cria e/ou recria o catálogo e schemas, garantindo um ambiente limpo para execução do pipeline.
Antes de criar, o processo remove (quando existente) o catálogo e seus schemas, evitando resíduos de execuções anteriores que possam impactar a consistência dos dados.

**Responsabilidades principais**

- Criar o catálogo `100cep_gateway`.
- Criar os schemas `staging`, `bronze`, `silver` e `gold`.
- Garantir que todos os objetos posteriores (volumes, tabelas, views) sejam criados neste catálogo.

***

## Ingestão de dados e staging: [02_download](.databricks/pipeline/notebooks/02_download.ipynb)

Nesta etapa é feita a ingestão física dos arquivos de dados para o ambiente Databricks, utilizando volumes do Unity Catalog para armazenamento centralizado.

### Volume e datasets

- Criação de um volume chamado `imdb` no schema `staging` para armazenar os arquivos de dados do projeto.
- Instalação e uso da biblioteca `kagglehub` para download direto de datasets hospedados no Kaggle.


### Fontes de dados

- Dataset principal: **Brazilian E‑Commerce Public Dataset by Olist** (pedidos, pagamentos, produtos, clientes, vendedores, entregas e avaliações).
- Dataset complementar: `datasets/ai_dataset/chargebacks_dataset.csv`, simulando chargebacks associados a transações de e‑commerce.


### Fluxo de staging

- Download do dataset Olist via `kagglehub`.
- Cópia dos arquivos CSV baixados para o volume `imdb` no schema `staging`, garantindo governança e disponibilidade dos dados para as próximas camadas.

***

## Camada Bronze — Dados raw estruturados: [03_bronze](.databricks/pipeline/notebooks/03_bronze.ipynb)

A camada **Bronze** realiza a ingestão inicial dos arquivos CSV para tabelas Delta, preservando a granularidade e o conteúdo dos dados originais, mas já em estrutura tabular padronizada.

### Objetivo da camada Bronze

- Carregar todos os arquivos CSV do diretório de staging para tabelas Delta.
- Manter o formato mais próximo do original, garantindo rastreabilidade total da origem dos dados.
- Adotar nomenclatura consistente com sufixo `_raw` para indicar dados brutos.


### Regras de ingestão

- Leitura automatizada de todos os arquivos no diretório de staging.
- Uso de cabeçalho e separador adequado ao padrão dos CSVs.
- Derivação do nome da tabela a partir do nome do arquivo, com regra para garantir prefixo `100cep_` quando necessário.


### Tabelas Bronze criadas

As seguintes tabelas foram criadas no schema `bronze`, cada uma relacionada a um arquivo CSV de origem:

- `100cep_customers_raw`
- `100cep_geolocation_raw`
- `100cep_order_items_raw`
- `100cep_order_payments_raw`
- `100cep_order_reviews_raw`
- `100cep_orders_raw`
- `100cep_products_raw`
- `100cep_sellers_raw`
- `100cep_product_category_name_translation_raw`
- `100cep_chargebacks_raw`

Essas tabelas formam a base histórica e auditável para as transformações nas camadas Silver e Gold.

***

## Camada Silver — Dados limpos e integrados: [04_silver](.databricks/pipeline/notebooks/04_silver.ipynb)

A camada **Silver** aplica regras de limpeza, padronização e enriquecimento sobre as tabelas Bronze, preparando dados prontos para análise e para modelagem dimensional.

### Transformações principais

- **Padronização de identificadores**
    - Normalização de chaves de pedidos, clientes, produtos e vendedores (remoção de espaços indesejados, padronização de case) para garantir consistência entre tabelas.
- **Tratamento de datas e horários**
    - Conversão de datas e timestamps relevantes para o fuso horário `America/Sao_Paulo`, com formato padronizado para análises temporais.
- **Normalização de status e tipos**
    - Tradução e padronização de status de pedidos e tipos de pagamento para português, com remoção de acentuação e uso de letras maiúsculas.
- **Conversão e tipagem de dados**
    - Conversão de colunas numéricas (parcelas, preços, fretes, valores totais) para tipos adequados (`INT`, `FLOAT`, `DECIMAL`).
- **Enriquecimentos**
    - Extração de prefixos de CEP dos clientes para análises geográficas e segmentações.


### Tabelas Silver temáticas

A partir das tabelas Bronze, foram criadas tabelas consolidadas na camada Silver:

- `100cep_pedidos`: visão de pedidos com status, datas e métricas principais.
- `100cep_pagamentos`: detalhes de pagamentos, tipos, parcelas e valores.
- `100cep_itens_pedidos`: itens de cada pedido, com produtos, quantidades e preços.
- `100cep_clientes`: cadastro de clientes com informações de localização e prefixo de CEP.

Essas tabelas servem como base para as dimensões e fato da camada Gold.

***

## Camada Gold — Modelo analítico (Star Schema): [05_gold](.databricks/pipeline/notebooks/05_gold.ipynb)

A camada **Gold** materializa o modelo analítico em um **esquema estrela**, com tabelas dimensionais e uma tabela fato central para análise de transações, risco e performance comercial.

### Dimensões

- **`dim_clientes`**
    - Registro único por cliente, com identificador e atributos de localização (incluindo prefixo de CEP).
- **`dim_vendedores`**
    - Dados consolidados de vendedores, permitindo análises de performance e cobertura geográfica.
- **`dim_pagamentos`**
    - Métodos de pagamento e atributos relacionados, incluindo classificação de risco por tipo de transação.
- **`dim_data`**
    - Tabela calendário com granularidade diária, contendo data, dia da semana, mês, ano e demais atributos de tempo.
- **`dim_geolocalizacao`**
    - Informações de cidades e geolocalização válidas e relevantes para o negócio.
- **`dim_chargebacks`**
    - Detalhamento de chargebacks, com motivos e respostas padronizadas, voltado para análises de risco e fraude.


### Tabela fato

- **`fato_transacoes`**
    - Consolida as transações realizadas, integrando pedidos, clientes, vendedores, pagamentos, valores totais e status de pedidos.
    - É a tabela central para análises de vendas, receita, comportamento do cliente, eficiência operacional e impacto de chargebacks.

As relações entre fato e dimensões seguem a modelagem de esquema estrela discutida previamente (fato_transacoes ligada a dim_clientes, dim_vendedores, dim_data, dim_geolocalizacao e dim_chargebacks).

***

## Linhagem de dados

A linhagem do pipeline pode ser resumida como:

- **Origem**
    - Kaggle CSV (dataset Olist) + `datasets/ai_dataset/chargebacks_dataset.csv`.
- **Bronze (raw)**
    - Arquivos CSV ingeridos em tabelas `*_raw` no schema `bronze`.
- **Silver (cleaned)**
    - Dados limpos, padronizados e enriquecidos em tabelas temáticas (`100cep_pedidos`, `100cep_pagamentos`, `100cep_itens_pedidos`, `100cep_clientes`).
- **Gold (analytics)**
    - Modelo dimensional com `dim_clientes`, `dim_vendedores`, `dim_pagamentos`, `dim_data`, `dim_geolocalizacao`, `dim_chargebacks` e `fato_transacoes`.

***

## Scripts do pipeline

Os scripts do projeto são organizados de forma sequencial, permitindo execução manual ou orquestração como *job* de ETL:


| Ordem | Script | Descrição |
| :-- | :-- | :-- |
| 1 | `./.databricks/pipeline/notebooks/01_preparacao.ipynb` | Criação do catálogo `100cep_gateway` e dos schemas `staging`, `bronze`, `silver` e `gold`. |
| 2 | `./.databricks/pipeline/notebooks/02_download.ipynb` | Criação de volume, download via `kagglehub` e cópia dos CSV para o volume de staging. |
| 3 | `./.databricks/pipeline/notebooks/03_bronze.ipynb` | Ingestão dos CSV para tabelas Delta `*_raw` na camada Bronze. |
| 4 | `./.databricks/pipeline/notebooks/04_silver.ipynb` | Limpeza, padronização, enriquecimento e criação das tabelas Silver temáticas. |
| 5 | `./.databricks/pipeline/notebooks/05_gold.ipynb` | Criação das dimensões e da `fato_transacoes` na camada Gold. |