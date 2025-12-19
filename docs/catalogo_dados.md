# MPV - Catálogo de Dados – Acidentes de Trânsito no Brasil (2017–2023)

## 1. Visão Geral

Este catálogo de dados descreve o conjunto de dados utilizado no MVP de Engenharia de Dados, cujo objetivo é analisar padrões temporais, geográficos e de gravidade dos acidentes de trânsito ocorridos em rodovias federais brasileiras entre os anos de 2017 e 2023.

Os dados foram processados por meio de um pipeline de dados implementado na plataforma Databricks, seguindo a arquitetura em camadas **RAW → BRONZE → SILVER → GOLD**, com persistência em formato **Delta Lake** e modelagem final em **Esquema Estrela**, visando análises analíticas e gerenciais.

---

## 2. Fonte dos Dados

- **Origem:** Kaggle  
- **Dataset:** Car Accidents in Brazil (2017–2023)  
- **Autor:** mlippo  
- **URL:**  
  https://www.kaggle.com/datasets/mlippo/car-accidents-in-brazil-2017-2023  

- **Instituição de origem dos dados:** Polícia Rodoviária Federal (PRF)  
- **Periodicidade:** Diária  
- **Cobertura temporal:** 2017 a 2023  
- **Cobertura geográfica:** Rodovias federais brasileiras  

Licença: 
O conjunto de dados é disponibilizado publicamente no Kaggle para fins educacionais e analíticos, respeitando os termos de uso da plataforma.

---

## 3. Linhagem dos Dados

### 3.1 Camada RAW
- Formato: CSV
- Conteúdo: Dados brutos conforme disponibilizados no Kaggle
- Local de armazenamento:  
  `/Volumes/workspace/default/raw_accident` (databricks)

Nenhuma transformação foi aplicada nesta camada.

---

### 3.2 Camada BRONZE
- Formato: Delta Lake
- Conteúdo: Persistência dos dados brutos em formato estruturado
- Local de armazenamento:  
  `/Volumes/workspace/default/bronze_accident`

Transformações aplicadas:
- Inferência automática de schema
- Persistência física dos dados

---

### 3.3 Camada SILVER
- Formato: Delta Lake
- Conteúdo: Dados tratados e enriquecidos
- Local de armazenamento:  
  `/Volumes/workspace/default/silver_accident`

Transformações aplicadas:
- Tratamento de valores nulos em atributos numéricos
- Enriquecimento temporal (ano, mês, dia)
- Padronização e consolidação dos atributos

---

### 3.4 Camada GOLD
- Formato: Delta Lake
- Conteúdo: Dados modelados para análise
- Modelo: Esquema Estrela
- Local de armazenamento:  
  `/Volumes/workspace/default/gold_accident`

Transformações aplicadas:
- Criação de dimensões
- Criação de tabela fato
- Geração de chaves substitutas
- Preparação para consultas analíticas

---

## 4. Modelo de Dados – Camada GOLD

O modelo final segue o padrão **Esquema Estrela**, composto por uma tabela fato central e três tabelas dimensão.

### Tabela Fato
- **Fato_Acidente**

### Tabelas Dimensão
- **Dim_Tempo**
- **Dim_Localizacao**
- **Dim_Acidente**

---

## 5. Catálogo de Tabelas 

### 5.1 Fato_Acidente

**Descrição:**  
Tabela fato central do modelo analítico, representando cada registro de acidente de trânsito ocorrido em rodovias federais brasileiras. Esta tabela consolida as principais métricas relacionadas à gravidade e impacto dos acidentes, permitindo análises quantitativas e cruzamentos com as dimensões de tempo, localização e características do acidente.

| Coluna | Tipo | Descrição | Domínio / Observações |
|------|------|-----------|-----------------------|
| id_tempo | bigint | Chave estrangeira da dimensão tempo | ≥ 0 |
| id_localizacao | bigint | Chave estrangeira da dimensão localização | ≥ 0 |
| id_caracteristica | bigint | Chave estrangeira da dimensão acidente | ≥ 0 |
| pessoas | double | Total de pessoas envolvidas no acidente | ≥ 0 |
| mortos | double | Número de mortos no acidente | ≥ 0 |
| feridos | double | Total de feridos | ≥ 0 |
| feridos_graves | double | Número de feridos graves | ≥ 0 |
| feridos_leves | double | Número de feridos leves | ≥ 0 |
| ilesos | double | Número de pessoas ilesas | ≥ 0 |
| veiculos | double | Número de veículos envolvidos | ≥ 0 |


### 5.2 Dim_Tempo

**Descrição:**  
Dimensão responsável por organizar e estruturar a informação temporal dos acidentes, possibilitando análises por data, ano, mês, dia e dia da semana, bem como a identificação de padrões e tendências ao longo do tempo.

| Coluna | Tipo | Descrição | Domínio |
|------|------|-----------|--------|
| id_tempo | bigint | Identificador único do tempo | ≥ 0 |
| data_inversa | date | Data do acidente | yyyy-MM-dd |
| ano | int | Ano do acidente | 2017–2023 |
| mes | int | Mês do ano | 1–12 |
| dia | int | Dia do mês | 1–31 |
| dia_semana | string | Dia da semana | Domingo a Sábado |


### 5.3 Dim_Localizacao

**Descrição:**  
Dimensão que descreve a localização geográfica e administrativa onde o acidente ocorreu, permitindo análises espaciais e regionais, bem como comparações entre rodovias, estados, municípios e unidades operacionais da PRF.

| Coluna | Tipo | Descrição | Domínio |
|------|------|-----------|--------|
| id_localizacao | bigint | Identificador único da localização | ≥ 0 |
| uf | string | Unidade federativa | Lista de UFs |
| br | string | Rodovia federal | BR-XXX |
| km | string | Quilômetro da rodovia | Texto |
| municipio | string | Município do acidente | Texto |
| latitude | double | Latitude geográfica | -90 a 90 |
| longitude | double | Longitude geográfica | -180 a 180 |
| regional | string | Regional da PRF | Texto |
| delegacia | string | Delegacia responsável | Texto |


### 5.4 Dim_Acidente

**Descrição:**  
Dimensão que consolida as características e o contexto do acidente, incluindo causa, tipo, gravidade, condições da via e fatores ambientais, possibilitando análises qualitativas e correlações entre esses fatores e a severidade dos acidentes.

| Coluna | Tipo | Descrição | Domínio |
|------|------|-----------|--------|
| id_caracteristica | bigint | Identificador da característica do acidente | ≥ 0 |
| causa_acidente | string | Causa principal do acidente | Categórico |
| tipo_acidente | string | Tipo de acidente | Categórico |
| classificacao_acidente | string | Classificação da gravidade | Leve / Grave / Fatal |
| fase_dia | string | Período do dia | Manhã / Tarde / Noite |
| sentido_via | string | Sentido da via | Categórico |
| condicao_metereologica | string | Condição climática | Categórico |
| tipo_pista | string | Tipo da pista | Simples / Dupla |
| tracado_via | string | Traçado da via | Reta / Curva |


---

## 6. Qualidade dos Dados

A análise de qualidade dos dados foi realizada sobre a camada gold no notebook [04_analise_qualidade_dados](https://github.com/diogokelmer/MVP3_Trabalho/blob/main/notebooks/com_output/04_analise_qualidade_dados.ipynb), por representar o conjunto final de dados utilizado nas análises e na resposta às perguntas de negócio deste MVP.

Inicialmente, foi realizada a verificação de volume, que indicou a presença de 463.152 registros na camada Gold. Esse valor é consistente com o volume observado nas camadas anteriores do pipeline (Bronze e Silver), evidenciando que não houve perda de registros durante os processos de transformação e modelagem dos dados.

Em seguida, foi conduzida a verificação de valores nulos nos principais atributos numéricos relacionados à gravidade dos acidentes, tais como número de mortos, feridos e veículos envolvidos. Os resultados mostraram ausência de valores nulos nesses campos, indicando que o tratamento aplicado na camada Silver foi eficaz e garantiu a completude dos dados para análise.

A análise de valores fora do domínio esperado também não identificou registros inválidos, como valores negativos para quantidades de mortos, feridos ou veículos. Esse resultado reforça a consistência lógica do conjunto de dados e sua adequação para análises quantitativas.

Por fim, foi realizada uma análise de distribuição básica (sanidade) dos indicadores de gravidade, considerando valores mínimos, máximos e médias. As estatísticas observadas apresentaram comportamento coerente com o fenômeno analisado, sem a presença de valores extremos inconsistentes ou incompatíveis com o contexto de acidentes de trânsito.

De forma geral, os resultados indicam que o conjunto de dados da camada Gold apresenta boa qualidade, consistência e integridade, estando apto para suportar as análises analíticas propostas e a resposta às perguntas de negócio definidas nos objetivos deste MVP.

- Valores ausentes em campos numéricos foram tratados com substituição por zero na camada Silver.
- A coluna de data já se encontrava em formato `date`, não sendo necessária conversão adicional.
- Não foram identificados valores fora do domínio esperado para os principais indicadores de gravidade.