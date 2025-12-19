# MVP – Análise de Acidentes de Trânsito no Brasil (2017–2023)

## Visão Geral

Este repositório contém o desenvolvimento de um MVP de Engenharia de Dados cujo objetivo é construir um pipeline de dados em nuvem para análise de acidentes de trânsito ocorridos no Brasil entre os anos de 2017 e 2023.

O projeto foi desenvolvido na plataforma **Databricks Community Edition**, utilizando **Delta Lake** para persistência dos dados e seguindo uma arquitetura em camadas (**RAW → BRONZE → SILVER → GOLD**), com modelagem final em **Esquema Estrela**, adequada para análises analíticas e gerenciais.

---

## Objetivo Geral

Analisar os acidentes de trânsito registrados no Brasil entre 2017 e 2023, identificando padrões temporais, geográficos e de gravidade, com o objetivo de descrever tendências e apoiar interpretações sobre fatores associados à ocorrência e severidade dos acidentes.

---

## Perguntas de Negócio

- Como evoluiu o número de acidentes ao longo dos anos (2017–2023)?
- Quais estados e municípios concentram mais acidentes?
- Existem padrões temporais (mês e dia da semana) associados a maior incidência?
- Como se distribuem os acidentes por tipo e por gravidade?

Todas as perguntas foram respondidas no notebook 05_solucao_problema

---

## Escopo

- **Base de dados:** `accidents_2017_to_2023_portugues.csv`
- **Fonte:** Kaggle – *Car Accidents in Brazil (2017–2023)*  
  https://www.kaggle.com/datasets/mlippo/car-accidents-in-brazil-2017-2023
- **Instituição de origem:** Polícia Rodoviária Federal (PRF)
- **Período analisado:** 2017 a 2023
- **Cobertura geográfica:** Rodovias federais brasileiras

---

## Arquitetura e Pipeline de Dados

O pipeline de dados foi estruturado da seguinte forma:

### RAW
- Armazenamento do arquivo CSV original, sem transformações.
- Objetivo: preservar os dados brutos conforme disponibilizados pela fonte.

### BRONZE
- Persistência dos dados em formato **Delta Lake**.
- Inferência de schema e estruturação inicial dos dados.

### SILVER
- Limpeza e enriquecimento dos dados.
- Tratamento de valores nulos.
- Enriquecimento temporal (ano, mês e dia).
- Padronização para análises posteriores.

### GOLD
- Modelagem dos dados em **Esquema Estrela**.
- Criação de tabela fato e dimensões.
- Preparação dos dados para consultas analíticas e resposta às perguntas de negócio.

---

## Modelagem de Dados

O modelo final é composto por:

- **Fato_Acidente**
- **Dim_Tempo**
- **Dim_Localizacao**
- **Dim_Acidente**

A descrição detalhada das tabelas, atributos, domínios e linhagem dos dados pode ser encontrada no **catálogo de dados**: [link](https://github.com/diogokelmer/MVP3_Trabalho/blob/main/docs/catalogo_dados.md)

---
## Análise de Qualidade dos Dados

A análise de qualidade dos dados foi realizada sobre a **camada GOLD** e pode ser encontrada no notebook 04_analise_qualidade_dados.

As principais verificações realizadas foram:

- **Volume de registros:** confirmação de que a quantidade de registros foi preservada ao longo das camadas do pipeline, sem perdas entre BRONZE, SILVER e GOLD.
- **Valores nulos:** verificação de campos críticos relacionados à gravidade dos acidentes (mortos, feridos, veículos), não sendo identificados valores nulos nesses atributos.
- **Valores fora do domínio:** validação de métricas numéricas para garantir ausência de valores negativos ou inconsistentes (por exemplo, número de mortos ou feridos inferior a zero).
- **Sanidade estatística:** análise básica de valores mínimos, máximos e distribuições para garantir coerência com o contexto do problema.

Os resultados indicaram que o conjunto de dados apresenta **boa qualidade geral**, sendo adequado para responder às perguntas de negócio propostas, sem a necessidade de exclusão de registros ou correções estruturais adicionais.

---

## Solução dos Problemas

A análise e solução completa pode ser encotnrada em 05_solucao_problema.

### Principais Resultados

#### Evolução dos Acidentes (2017–2023)

Observou-se uma **redução progressiva no número total de acidentes** ao longo do período analisado. O ano de 2017 apresentou o maior volume de registros, seguido por uma queda significativa em 2018. Entre 2019 e 2022, os valores se mantiveram relativamente estáveis, com pequenas oscilações. Em 2023, nota-se uma nova redução mais acentuada, possivelmente influenciada por fatores externos como mudanças de comportamento no trânsito, políticas públicas de segurança viária ou diferenças na consolidação dos dados do ano mais recente.


#### Distribuição Geográfica

A análise por estado revelou que **Minas Gerais, Santa Catarina e Paraná** concentram o maior número de acidentes no período analisado, seguidos por Rio de Janeiro, Rio Grande do Sul e São Paulo. Esse resultado está associado tanto à extensão da malha rodoviária federal nesses estados quanto à intensidade do tráfego. No nível municipal, destacam-se **Curitiba, Palhoça e Brasília** como os municípios com maior número de registros. De forma geral, os municípios com maior incidência estão localizados em regiões metropolitanas ou em importantes corredores logísticos, reforçando a relação entre volume de tráfego e ocorrência de acidentes.


#### Padrões Temporais

A análise mensal indica maior concentração de acidentes nos meses do **primeiro e do meio do ano**, com picos observados especialmente entre janeiro, julho e agosto. Já os meses de setembro a novembro apresentam os menores volumes relativos de ocorrências. Quanto ao dia da semana, observa-se uma concentração significativa de acidentes nos **finais de semana**, com sábado e domingo liderando o ranking. Esse padrão sugere maior exposição ao risco nesses dias, possivelmente associada ao aumento do fluxo rodoviário recreativo e a comportamentos de maior risco no trânsito.


#### Tipo e Gravidade dos Acidentes

Os tipos de acidentes mais frequentes são as **colisões traseiras**, seguidas por saídas de leito carroçável e colisões transversais. Esses eventos representam a maior parte dos registros e, em geral, resultam principalmente em vítimas feridas, com menor letalidade proporcional. Por outro lado, ao analisar os acidentes com **vítimas fatais**, destacam-se principalmente as **colisões frontais** e os **atropelamentos de pedestres**. Embora menos frequentes, esses tipos apresentam maior gravidade, indicando que a severidade dos acidentes não está necessariamente relacionada à sua frequência, mas sim à sua natureza e ao nível de exposição das vítimas.

---

## Autoavaliação

Os objetivos definidos no início do trabalho foram majoritariamente atingidos. Foi possível construir um pipeline completo de dados em nuvem, desde a ingestão até a análise final, utilizando boas práticas de engenharia de dados, persistência em Delta Lake e modelagem dimensional em esquema estrela.

Entre as principais dificuldades encontradas durante o desenvolvimento do MVP, destacam-se:

- A configuração inicial da persistência de tabelas Delta na Databricks Community Edition.
- A necessidade de refatoração da camada GOLD para garantir a persistência adequada das dimensões e da tabela fato.
- Ajustes iterativos na modelagem para viabilizar consultas analíticas consistentes e alinhadas às perguntas de negócio.

Apesar desses desafios, o processo contribuiu significativamente para o aprofundamento prático em pipelines de dados, modelagem dimensional e análise exploratória orientada a negócio.

---

## Trabalhos Futuros

Como possíveis evoluções deste MVP, destacam-se:

- Inclusão de dados de tráfego ou frota veicular para normalização dos indicadores de acidentes.
- Desenvolvimento de modelos preditivos para estimar risco de acidentes com base em características temporais e geográficas.
- Criação de dashboards interativos para visualização dos resultados.
- Ampliação do modelo de dados com informações climáticas ou de infraestrutura viária.

