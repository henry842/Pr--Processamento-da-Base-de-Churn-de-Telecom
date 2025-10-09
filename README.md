# Pré-Processamento da Base de Churn de Telecom

**Aluno:** Henry

---

## 🔹 Proposta da Atividade

Esta atividade tem como objetivo aplicar os conhecimentos de **pré-processamento de dados** em uma base de churn de clientes de telecomunicações. A ideia é preparar a base para análises e modelagem preditiva, utilizando técnicas de limpeza de dados, tratamento de valores faltantes, padronização de variáveis categóricas, renomeação de colunas e visualizações para análise exploratória.

> Esta é uma atividade prática de pré-modelagem, que visa consolidar os conceitos aprendidos em sala de aula. O pré-processamento é essencial para que os dados possam ser usados corretamente em análises e modelos preditivos. Ele corrige inconsistências, preenche ou remove valores ausentes e padroniza os dados, garantindo resultados confiáveis.

---

## 1️⃣ Objetivo

Garantir que os dados estejam limpos, consistentes e prontos para modelagem preditiva, corrigindo inconsistências, preenchendo ou removendo valores ausentes e padronizando os dados.

> Preparar os dados desta forma evita erros em cálculos, análises estatísticas e modelagem preditiva. Ajuda também a manter padrões consistentes nas colunas e categorias.

---

## 2️⃣ Bibliotecas utilizadas

As principais bibliotecas utilizadas foram:

* **pandas**: para manipulação e limpeza de dados; leitura, transformação e filtragem.
* **numpy**: para operações numéricas.
* **matplotlib & seaborn**: para visualização de dados e análise de distribuições, padrões e outliers.
* **missingno**: para visualizar valores faltantes de forma gráfica, ajudando a identificar rapidamente padrões de ausência de dados.

```python
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import missingno as msno
```

---

## 3️⃣ Carregamento da base e ajustes iniciais

O arquivo CSV foi carregado utilizando o separador `;`. As colunas `Pagamento_Mensal` e `Total_Pago` foram convertidas para numérico, transformando valores inválidos em `NaN`. A coluna `Idoso` foi convertida para inteiro (`int64`).

```python
df = pd.read_csv("CHURN_TELECON_MOD08_TAREFA.csv", delimiter=';')
df['Pagamento_Mensal'] = pd.to_numeric(df['Pagamento_Mensal'], errors='coerce')
df['Total_Pago'] = pd.to_numeric(df['Total_Pago'], errors='coerce')
df['Idoso'] = df['Idoso'].astype('int64')
```

> Ajustar tipos de dados dessa forma evita erros futuros em cálculos, substituição de valores faltantes e modelagem.

---

## 4️⃣ Verificação de valores faltantes

Foi calculado o percentual de valores ausentes em cada coluna para decidir se seria melhor excluir linhas ou substituir valores.

```python
missing_percent = df.isnull().mean() * 100
print(missing_percent)
```

### Visualização de valores nulos

```python
fig, axes = plt.subplots(3, 1, figsize=(12, 18))
msno.bar(df, ax=axes[0], fontsize=12, color='skyblue', sort='descending')
axes[0].set_title("Percentual de valores faltantes por coluna", fontsize=16)
msno.matrix(df, ax=axes[1], fontsize=12, sparkline=True)
axes[1].set_title("Distribuição de valores faltantes (matriz)", fontsize=16)
msno.heatmap(df, ax=axes[2], fontsize=12)
axes[2].set_title("Correlação entre valores faltantes nas colunas", fontsize=16)
plt.tight_layout()
plt.show()
```

> Essas visualizações ajudam a tomar decisões sobre exclusão de linhas ou substituição de valores faltantes.

---

## 5️⃣ Tratamento de valores faltantes

### 5.1 Exclusão de linhas essenciais

As colunas `Churn` e `Genero` são essenciais para análise e modelagem. Linhas com valores nulos nessas colunas foram removidas.

```python
df = df.dropna(subset=['Churn', 'Genero'])
```

### 5.2 Substituição de valores

* **Numéricas:** `Pagamento_Mensal` e `Total_Pago` foram substituídas pela mediana, reduzindo o efeito de outliers e mantendo a distribuição natural.

```python
df['Pagamento_Mensal'] = df['Pagamento_Mensal'].fillna(df['Pagamento_Mensal'].median())
df['Total_Pago'] = df['Total_Pago'].fillna(df['Total_Pago'].median())
```

* **Categóricas:** `Servico_Internet` foi substituída pela moda, preservando a distribuição natural sem criar categorias artificiais.

```python
df['Servico_Internet'] = df['Servico_Internet'].fillna(df['Servico_Internet'].mode()[0])
```

---

## 6️⃣ Visualizações após tratamento

* **Distribuição do Pagamento Mensal:** verificou-se se a substituição pela mediana manteve a distribuição e detectou possíveis outliers.

```python
sns.histplot(df['Pagamento_Mensal'], kde=True)
plt.title("Distribuição de Pagamento Mensal")
plt.show()
```

* **Distribuição do Tipo de Internet:** mostrou a frequência das categorias, justificando o uso da moda.

```python
sns.countplot(x=df['Servico_Internet'], order=df['Servico_Internet'].value_counts().index)
plt.title("Distribuição de Tipo de Internet")
plt.show()
```

---

## 7️⃣ Padronização de valores inconsistentes

Espaços extras foram removidos, letras maiúsculas/minúsculas uniformizadas e inconsistências corrigidas. Todas as colunas categóricas foram padronizadas para Title Case, garantindo consistência.

```python
df['Servico_Internet'] = df['Servico_Internet'].str.strip().str.lower()
df['Servico_Internet'] = df['Servico_Internet'].replace({'dsl': 'DSL','fiber optic': 'Fiber Optic','no': 'No'})
cols_cat = df.select_dtypes(include='object').columns
for col in cols_cat:
    df[col] = df[col].astype(str).str.strip().str.title()
```

---

## 8️⃣ Renomeação de colunas

As colunas foram renomeadas para facilitar leitura e manter um padrão consistente em português.

```python
df = df.rename(columns={
    'customerID': 'Cliente_ID',
    'Genero': 'Genero',
    'Idoso': 'Eh_Idoso',
    'Casado': 'Eh_Casado',
    'Dependents': 'Tem_Dependentes',
    'Tempo_como_Cliente': 'Meses_Como_Cliente',
    'PhoneService': 'Servico_Telefone',
    'Servico_Internet': 'Tipo_Internet',
    'Servico_Seguranca': 'Servico_Seguranca',
    'Suporte_Tecnico': 'Suporte_Tecnico',
    'StreamingTV': 'Servico_Streaming',
    'Tipo_Contrato': 'Tipo_Contrato',
    'PaymentMethod': 'Metodo_Pagamento',
    'Pagamento_Mensal': 'Valor_Mensal',
    'Total_Pago': 'Total_Pago',
    'Churn': 'Churn'
})
```

---

## 9️⃣ Conferência final

Verificou-se que não existem valores faltantes, os tipos de dados estão corretos e as estatísticas descritivas foram conferidas.

```python
print(df.isnull().sum())
print(df.info())
print(df.head())
print(df.describe(include='all'))
```

---

## 🔟 Observações finais

* Mediana para colunas numéricas reduz efeito de outliers.
* Moda para colunas categóricas preserva a distribuição natural.
* Exclusão de linhas aplicada apenas em colunas essenciais (`Churn`, `Genero`).
* Pré-processamento garante dados limpos e consistentes, prontos para análise e modelagem preditiva.
* Visualizações ajudam a justificar decisões de limpeza e substituição de valores faltantes.
