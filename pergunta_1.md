# KDD - Análise da Relação entre Unidades de Conservação Federais, Municípios Turísticos e Queimadas

## Descrição do Problema

### Problema Central

Este projeto investiga a **relação complexa entre Unidades de Conservação (UCs) federais, atividade turística municipal e incidência de queimadas** no território brasileiro. O estudo utiliza metodologia KDD (Knowledge Discovery in Databases) para extrair conhecimento a partir de dados governamentais sobre conservação ambiental, turismo e focos de queimada.

### Perguntas de Pesquisa

**Pergunta Principal:**

- _Qual a relação entre as Unidades de Conservação federais perto dos municípios turísticos e as queimadas na região?_

**Perguntas Secundárias:**

- Municípios turísticos com mais UCs apresentam maior ou menor incidência de queimadas?
- A proximidade geográfica entre UCs e turismo municipal influencia os focos de queimada?
- Existem padrões regionais ou por bioma nessa relação?
- O tipo de UC (Proteção Integral vs. Uso Sustentável) interfere na dinâmica turismo-queimadas?

### Hipóteses Exploradas

**H1 - Correlação Positiva UC-Turismo:**
Municípios com maior número de Unidades de Conservação tendem a ser mais atrativos turisticamente devido ao valor ecológico e paisagístico.

**H2 - Correlação Negativa Turismo-Queimadas:**
Municípios turísticos apresentam menor incidência de queimadas devido a maior fiscalização e interesse econômico na preservação ambiental.

**H3 - Efeito Moderador das UCs:**
A presença de UCs em municípios turísticos atua como fator protetor contra queimadas através de monitoramento intensificado.

**H4 - Variação por Bioma:**
A relação UC-turismo-queimadas varia significativamente entre biomas, sendo mais pronunciada no Cerrado e Amazônia.

### Justificativa da Escolha

**Relevância Acadêmica:**

- Integração de dados de múltiplas fontes governamentais para análise multidimensional
- Aplicação de metodologia KDD em contexto de sustentabilidade e gestão ambiental
- Contribuição para compreensão da relação turismo-conservação-degradação ambiental

**Relevância Prática:**

- Subsidiar políticas públicas de turismo sustentável
- Orientar estratégias de prevenção de queimadas em regiões turísticas
- Identificar municípios prioritários para investimento em conservação

**Objetivos do Projeto de Extensão:**

- Desenvolver competências em mineração de dados aplicada a problemas socioambientais
- Promover uso de dados abertos governamentais para pesquisa
- Gerar conhecimento aplicável à gestão territorial sustentável

## Solução Proposta através da Mineração de Dados

### Abordagem Metodológica

Aplicação do processo **KDD (Knowledge Discovery in Databases)** em 9 etapas estruturadas:

1. **Seleção de Dados**: Integração de 4 datasets governamentais
2. **Pré-processamento**: Limpeza, normalização e tratamento de inconsistências
3. **Transformação**: Criação de variáveis derivadas e agregações
4. **Mineração**: Análise de correlações, testes estatísticos e clustering
5. **Interpretação**: Validação de hipóteses e extração de insights
6. **Análise Exploratória**: Visualizações e análise descritiva
7. **Análise Geográfica**: Estudo de proximidade espacial
8. **Validação Estatística**: Testes de significância e robustez
9. **Conclusões**: Síntese dos padrões identificados

### Técnicas de Mineração Utilizadas

- **Análise de Correlação** (Pearson e Spearman)
- **Testes de Hipóteses** (Mann-Whitney U)
- **Análise Geoespacial** (cálculo de distâncias geodésicas)
- **Clustering por Quartis** (segmentação de municípios)
- **Análise Multivariada** (correlações cruzadas)

## Principais Etapas do Processo KDD

### 1. Seleção de Dados

**Datasets Utilizados:**

| Dataset                   | Fonte      | Registros       | Período   | Variáveis-Chave                    |
| ------------------------- | ---------- | --------------- | --------- | ---------------------------------- |
| **CNUC**                  | ICMBio/MMA | 4.434 UCs       | 1959-2019 | Categoria, Bioma, Município, Grupo |
| **Municípios Turísticos** | MTur       | 924 municípios  | 2019      | Visitantes, Empregos, Arrecadação  |
| **Queimadas em UCs**      | ICMBio     | 157 UCs         | 2015-2022 | Área queimada anual por UC         |
| **Focos de Queimada**     | INPE       | 1.546.646 focos | 2015-2022 | Coordenadas, FRP, Bioma, Data      |

### 2. Atributos Definidos

**Variáveis Principais:**

- `TOTAL_VISITANTES`: Soma de visitantes nacionais e internacionais
- `NUM_UCS`: Quantidade de UCs por município
- `NUM_FOCOS`: Número de focos de queimada por município
- `FOCOS_PROXIMOS`: Focos num raio geográfico específico
- `BIOMA_PRINCIPAL`: Bioma predominante no município

**Variáveis Derivadas:**

- `DENSIDADE_TURISTICA`: Visitantes/estabelecimentos
- `EFICIENCIA_UC`: UCs/área municipal
- `RISCO_QUEIMADA`: Focos/área total
- `QUARTIL_VISITANTES`: Segmentação por volume turístico

### 3. Pré-processamento

**Tratamento de Dados:**

```python
# Normalização de nomes (remoção de acentos, padronização)
df['MUNICIPIO_CLEAN'] = df['MUNICIPIO'].str.lower().apply(unidecode)

# Tratamento de valores ausentes
df['VISITANTES'].fillna(0, inplace=True)
df = df.dropna(subset=['MUNICIPIO', 'UF'])

# Filtros temporais
uc_df = uc_df[uc_df['Ano de Criação'] <= 2019]  # UCs até 2019
municipios_df = municipios_df[municipios_df['QUANTIDADE_ ESTABELECIMENTOS'] > 0]
```

**Problemas Resolvidos:**

- Inconsistências de grafia em nomes de municípios
- Valores nulos em variáveis turísticas
- Duplicatas por explosão de municípios multi-UC
- Padronização de códigos UF
- Filtros temporais para consistência

### 4. Análise Exploratória da Base

**Estatísticas Descritivas:**

| Métrica       | Municípios Turísticos | UCs Federais   | Focos de Queimada |
| ------------- | --------------------- | -------------- | ----------------- |
| **Total**     | 924 municípios        | 4.434 UCs      | 1.546.646 focos   |
| **Estados**   | 26 UFs + DF           | 27 UFs         | 27 UFs            |
| **Período**   | Snapshot 2019         | 1959-2019      | 2015-2022         |
| **Cobertura** | 16,6% dos municípios  | 334 municípios | 2.851 municípios  |

**Distribuições Encontradas:**

- **Visitantes**: Distribuição log-normal (mediana: 25.681)
- **UCs por município**: Distribuição exponencial (média: 1,8)
- **Focos de queimada**: Alta variabilidade (0-32.800 por município)
- **Biomas**: Mata Atlântica (37%), Cerrado (28%), Amazônia (18%)

### 5. Transformação dos Dados

**Integrações Realizadas:**

```python
# Junção principal: Municípios + UCs + Turismo
dataset_final = pd.merge(municipios_df, uc_df, on='MUNICIPIO_CLEAN', how='inner')

# Agregação de queimadas por município
queimadas_municipio = queimadas_df.groupby('Municipio_Clean').agg({
    'FRP': ['count', 'sum', 'mean'],
    'Latitude': 'mean',
    'Longitude': 'mean'
}).reset_index()

# Análise de proximidade geográfica
proximidade_df = calcular_proximidade_otimizada(municipios_df, queimadas_df, raio_km=100)
```

**Agregações Criadas:**

- Soma de visitantes por município-UC
- Contagem de UCs por tipo e grupo
- Densidade de focos por área municipal
- Coordenadas médias para análise espacial

### 6. Técnicas Utilizadas e Justificativas

#### 6.1 Análise de Correlação (Associação)

**Técnica**: Coeficientes de Pearson e Spearman

- Pearson: Relação linear entre variáveis contínuas, sensível a outliers
- Spearman: Relação monotônica (não linear) entre variáveis ordinais ou contínuas, robusto a outliers

**Justificativa**: Identificar relações lineares e monotônicas entre variáveis contínuas
**Aplicação**:

- Correlação UC-Turismo: r = 0,432 (moderada)
- Correlação UC-Queimadas: r = 0,161 (fraca)
- Correlação Turismo-Queimadas: r = -0,019 (muito fraca)

#### 6.2 Testes de Significância

**Técnica**: Teste Mann-Whitney U (compara duas amostras independentes para avaliar se uma distribuição tende a ter valores maiores que a outra)
**Justificativa**: Comparar grupos sem assumir normalidade dos dados
**Aplicação**: Comparação municípios com muitas vs. poucas UCs

#### 6.3 Análise Geoespacial

**Técnica**: Distâncias geodésicas (biblioteca GeoPy), estimativa de localização
**Justificativa**: Superar limitação da análise por contenção municipal
**Aplicação**:

```python
# Cálculo de proximidade real
dist = geodesic((lat_centro, lon_centro), (foco['Latitude'], foco['Longitude'])).kilometers
```

#### 6.4 Segmentação por Quartis (Clustering)

**Técnica**: Divisão em quartis de visitantes (Quartis é uma técnica de clustering simples, separando os dados em 4 grupos iguais)
**Justificativa**: Identificar padrões por intensidade turística
**Resultados**: Q4 (alto turismo) = 689 focos médios vs. Q1 (baixo turismo) = 488 focos

#### 6.5 Análise Comparativa Dupla

**Técnica**: Abordagem contenção (municipal, valores absolutos) vs. proximidade geográfica (raios, valores estimados)
**Justificativa**: Validar robustez dos achados com metodologias complementares
**Inovação**: Primeira abordagem usa contenção municipal, segunda usa raios geográficos

### 7. Análise das Regras Obtidas

#### Regra 1: Correlação UC-Turismo (CONFIRMADA)

```txt
SE município possui mais UCs
ENTÃO tende a ter mais visitantes (r = 0,432, p < 0,001)
```

**Confiança**: 95% | **Interpretação**: UCs são atrativos turísticos efetivos

#### Regra 2: Paradoxo UC-Queimadas (DESCOBERTA)

```txt
SE município possui mais UCs
ENTÃO tende a ter mais focos detectados (r = 0,161, p < 0,01)
```

**Confiança**: 95% | **Interpretação**: Maior monitoramento = maior detecção

#### Regra 3: Neutralidade Turismo-Queimadas (REFUTAÇÃO H2)

```txt
SE município é mais turístico
ENTÃO não necessariamente tem menos queimadas (r = -0,019, p = 0,54)
```

**Confiança**: 95% | **Interpretação**: Turismo não protege contra queimadas

#### Regra 4: Efeito Bioma (CONFIRMADA)

```txt
SE município está no Cerrado
ENTÃO tem 3x mais focos que Mata Atlântica
```

**Confiança**: 99% | **Interpretação**: Variação regional significativa

#### Regra 5: Proximidade Geográfica (NOVA DESCOBERTA)

```txt
SE análise por proximidade (100km) vs. contenção municipal
ENTÃO correlações se mantêm fracas (r = 0,009 vs. -0,019)
```

**Confiança**: 95% | **Interpretação**: Método não altera conclusões centrais

## Resultados e Descobertas

### Principais Achados

1. **Confirmação H1**: UCs atraem turismo (correlação moderada)
2. **Refutação H2**: Turismo não reduz queimadas significativamente
3. **Paradoxo**: UCs correlacionam positivamente com queimadas (efeito detecção)
4. **Confirmação H4**: Forte variação por bioma (Cerrado > Amazônia > Mata Atlântica)
5. **Descoberta**: Proximidade geográfica não altera padrões fundamentais

### Implicações Práticas

- **Gestão**: UCs são efetivos para turismo, mas insuficientes para prevenção isolada
- **Política**: Necessidade de estratégias integradas turismo-conservação-prevenção
- **Monitoramento**: Intensificar detecção não significa aumentar queimadas reais

## Configuração do Ambiente

### Fontes dos Dados

- **CNUC/ICMBio**: `https://dados.gov.br/dados/conjuntos-dados/unidadesdeconservacao`
- **Municípios Turísticos**: `https://dados.gov.br/dados/conjuntos-dados/categorizacao`
- **Queimadas em UCs**: `https://dados.gov.br/dados/conjuntos-dados/areas-queimadas-em-unidades-de-conservacao-federais`
- **Focos INPE**: `https://terrabrasilis.dpi.inpe.br/queimadas/bdqueimadas/#exportar-dados`

### Ambiente de Desenvolvimento

```bash
# Criação do ambiente virtual
python -m venv venv
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate  # Windows

# Dependências instaladas automaticamente pelo notebook
# pandas, numpy, matplotlib, seaborn, scipy, geopy, openpyxl, xlrd
```

### Arquivos Principais

- `KDD_UCs_pergunta_1_com_estimativa_de_localizacao.ipynb`: Versão com análise geográfica
- `datasets/`: Pasta com todos os datasets utilizados

---

**Projeto desenvolvido para:** Disciplina de Inteligência Artificial II - UNIVALI  
**Metodologia:** Knowledge Discovery in Databases (KDD)  
**Período:** 2025  
**Linguagem:** Python | **Ambiente:** Jupyter Notebook
**Autores:**

- Jonathas de Oliveira Meine
- Mateus Barbosa
- Mateus José da Silva
- Matheus de Oliveira Rocha
- Rodrigo Faistauer
