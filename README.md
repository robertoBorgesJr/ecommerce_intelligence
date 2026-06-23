# 🛒 E-Commerce Intelligence Pipeline: Arquitetura Medallion em Databricks Serverless

Este projeto implementa um pipeline de Engenharia de Dados completo e robusto de ponta a ponta, utilizando a arquitetura medallion sob a infraestrutura Databricks Serverless e governado pelo Unity Catalog. 

O ecossistema ingere e processa dados comportamentais (*clickstream*) e dados financeiros transacionais (*sales*) de forma incremental, aplicando regras de qualidade de dados, tratamento de desvios de esquemas e gerando dashboards analíticos sobre comportamento de clientes e sazonalidade financeira.

---

## 📐 Desenho da Arquitetura de Dados

O fluxo foi desenhado seguindo as melhores práticas do ecossistema Lakehouse:



1. **Landing Zone (Volumes do Unity Catalog):** Arquivos brutos gerados continuamente em formato JSON (`clickstream`) e transações monetárias (`sales`).
2. **Camada Bronze (Raw Ingestion):** Ingestão puramente incremental orientada a eventos usando o **Databricks Auto Loader (`cloudFiles`)**. Mapeamento automático de tipos de dados complexos (`STRUCT`) e mitigação de perdas usando a coluna especial `_rescued_data`.
3. **Camada Silver (Trusted & Quality):** Higienização, tipagem estrita e deduplicação de registros. Aplicação do padrão de Quarentena para isolar registros sem chaves operacionais sem derrubar o pipeline principal.
4. **Camada Gold (Analytics & Business):** Modelagem dimensional contendo tabelas de Dimensão (`dim_users_activity`), Fato (`fct_sales`) e Tabelas Agregadas (`agg_faturamento_pagamento`) otimizadas para consumo de Business Intelligence.

---

## 🛠️ Tecnologias e Recursos Utilizados

* **Apache Spark / PySpark:** Motor de processamento distribuído.
* **Databricks Serverless:** Ambiente unificado de execução e computação elástica.
* **Delta Lake:** Camada de armazenamento ACID com suporte a evolução de schema (`mergeSchema`).
* **Databricks Auto Loader (`cloudFiles`):** Processamento de arquivos novos em tempo de execução com inferência assíncrona de tipos.
* **Unity Catalog:** Governança centralizada de dados, schemas e Volumes.
* **Python Faker:** Simulação realista de dados em lote.

---

## 📊 Insights de Negócio Produzidos (Dashboards)

Os dados unificados na camada Gold foram acoplados a um **Databricks Dashboard** para consumo executivo. As principais métricas monitoradas são:

### 📉 Taxa de Abandono de Carrinho
Mapeia a eficiência do funil de conversão calculando a relação entre usuários que adicionaram itens ao carrinho em `dim_users_activity` e as compras efetivadas na tabela `fct_sales`. Gera listas automatizadas para o time de marketing disparar campanhas de recuperação de receita.

### ⏱️ Análise de Sazonalidade (Heatmap)
Cruzamento bidimensional de *Dia da Semana vs. Hora do Dia*, pintando os momentos de maior tráfego financeiro e orientando janelas seguras de manutenção e picos de investimento em anúncios digitais.

### 💳 Share de Meios de Pagamento & Ticket Médio
Mapeamento do faturamento acumulado e representatividade (Share %) de cada modalidade (Pix, Cartão de Crédito, Boleto), facilitando negociações de taxas bancárias com base no volume financeiro real do negócio.

---

## 📁 Estrutura do Repositório

```text
├── config/
│   └── schema_configs.ipynb      # Centralização de caminhos, checkpoints e variáveis do Unity Catalog
├── data_generator.ipynb          # Script de simulação de dados contínuos com sazonalidade e anomalias
├── 01_bronze_ingestion.ipynb     # Ingestão de streaming incremental via Auto Loader
├── 02_silver_trusted.ipynb      # Deduplicação, higienização, tipagem e Quarentena de Qualidade
├── 03_gold_modeling.ipynb        # Criação das tabelas fato, dimensão e views executivas
└── README.md                     # Documentação oficial do projeto