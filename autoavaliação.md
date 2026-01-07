<h1 align="center">Autoavaliação</h1>

<h2 align="center">Objetivos atingidos</h1>

Este MVP cumpriu os principais objetivos definidos para a sprint, tanto do ponto de vista acadêmico quanto de portfólio profissional. 

- Implementar um **pipeline de dados de ponta a ponta em ambiente cloud (Databricks)**, usando Spark e Delta Lake para processamento e armazenamento. 
- Estruturar as camadas **Bronze → Silver → Gold** com dados de e-commerce e chargebacks, seguindo boas práticas de arquitetura Lakehouse e Engenharia de Dados. 
- Modelar a camada Gold em **esquema estrela**, com `fato_transacoes` e dimensões de clientes, vendedores, pagamentos, data, geolocalização e chargebacks. 
- Documentar o projeto para uso como **peça de portfólio**, incluindo README, documentação de ETL, catálogo de dados e esta autoavaliação. 

---

<h2 align="center">Dificuldades encontradas</h1>

Ao longo do desenvolvimento, surgiram desafios típicos de projetos de Engenharia de Dados em ambiente cloud, além do aprendizado prático de novas ferramentas. 

- **Aprender, na prática, o uso do Databricks como plataforma de cloud**  
  Foi necessário entender conceitos de catálogo, schemas, volumes, clusters e Delta Lake, indo além do uso de notebooks isolados para um uso mais arquitetural da plataforma. 

- **Limitações de recursos e de ambiente**  
  As restrições de ambiente reduziram a possibilidade de testar cenários mais pesados de performance, tuning de consultas, experimentos com volumes maiores e configurações avançadas de cluster. 

- **Foco da sprint em cargas batch**  
  A ingestão foi implementada em modo batch, sem explorar streaming ou cenários near real-time, embora esses cenários sejam muito relevantes para o contexto de um gateway de pagamentos. 

- **Ausência de orquestração completa e data quality automatizado**  
  O pipeline ainda não está integrado a uma esteira de jobs com monitoramento, logs e alertas, nem possui um framework formal de data quality com regras declarativas e relatórios automáticos por camada. 

---

<h2 align="center">Pontos fortes</h1>

Mesmo com as limitações, alguns pontos se destacam positivamente no escopo específico de Engenharia de Dados. 

- **Arquitetura em camadas clara e explicável**  
  A divisão Bronze/Silver/Gold, combinada com o uso de Delta Lake e Unity Catalog, facilita explicar o pipeline para arquitetos, engenheiros e recrutadores técnicos. 

- **Modelo analítico bem definido (star schema)**  
  A existência de uma fato central e dimensões temáticas torna as consultas mais simples, reduz a complexidade de joins e aproxima o projeto do dia a dia de times de BI e Analytics. 

- **Linhagem e organização por etapas de ETL**  
  Cada script e camada tem uma responsabilidade clara (preparação, download, bronze, silver, gold), o que ajuda na depuração, reprocessamento e entendimento da linhagem dos dados. 

- **Documentação pensada para portfólio**  
  A preocupação com README, documentação de ETL, catálogo e autoavaliação reforça a habilidade de comunicar arquitetura e decisões técnicas, algo essencial para Engenharia de Dados moderna. 

---

<h2 align="center">Aprendizados</h1>

O desenvolvimento deste MVP trouxe aprendizados específicos para atuação como engenheiro de dados. 

- **Pensar dados como produto**  
  Cada camada e tabela foi tratada como um produto de dados, com um público-alvo e um caso de uso claro, o que influencia diretamente decisões de schema, granularidade e documentação. 

- **Separação de responsabilidades no pipeline**  
  A organização em scripts por etapa (preparação, ingestão, transformação, modelagem) reforçou a importância de pipelines modulares, mais fáceis de evoluir e orquestrar. 

- **Uso disciplinado de Spark e Lakehouse**  
  A prática reforçou a diferença entre processamento local e distribuído, e a importância de schema, tipos e governança (catálogo, camadas) para evitar problemas futuros. 

---

<h2 align="center">Próximos passos</h1>

Como evolução natural deste MVP, alguns próximos passos alinhados ao papel de Engenharia de Dados são: 

- Migrar a execução para um fluxo **orquestrado e monitorado**, com jobs, dependências e alertas. 
- Incorporar **streaming** para lidar com eventos transacionais em tempo quase real. 
- Adotar uma camada formal de **data quality** com métricas, thresholds e relatórios recorrentes. 
- Realizar **tuning de performance** e análise de custo em cenários com dados maiores e configurações diferentes de cluster. 

---