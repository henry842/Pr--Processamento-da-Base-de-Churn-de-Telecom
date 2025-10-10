Aluno: Henry
🔹 Proposta da Atividade

Esta atividade tem como objetivo aplicar os conhecimentos de pré-processamento de dados em uma base de churn de clientes de telecomunicações. O objetivo é preparar a base para análises e modelagem preditiva, utilizando técnicas de:

limpeza de dados,

tratamento de valores faltantes,

padronização de variáveis categóricas,

renomeação de colunas,

visualizações para análise exploratória.

O pré-processamento é essencial para que os dados possam ser usados corretamente em análises e modelos preditivos. Ele corrige inconsistências, substitui ou preenche valores ausentes e padroniza os dados, garantindo resultados confiáveis.

1️⃣ Objetivo

Garantir que os dados estejam limpos, consistentes e prontos para modelagem preditiva, corrigindo inconsistências, preenchendo valores ausentes com técnicas apropriadas (mediana e moda) e padronizando as colunas e categorias.

Preparar os dados dessa forma evita erros em cálculos, análises estatísticas e modelagem preditiva.

2️⃣ Bibliotecas utilizadas

As principais bibliotecas utilizadas foram:

pandas: manipulação e limpeza de dados; leitura, transformação e filtragem.

numpy: operações numéricas.

matplotlib & seaborn: visualização de dados e análise de distribuições, padrões e outliers.

missingno: visualização de valores faltantes de forma gráfica, ajudando a identificar rapidamente padrões de ausência de dados.

import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import missingno as msno

3️⃣ Carregamento da base e ajustes iniciais

O arquivo CSV foi carregado utilizando o separador ;. As colunas Pagamento_Mensal e Total_Pago foram convertidas para numérico com pd.to_numeric(..., errors='coerce').

Obs.: A conversão com errors='coerce' transforma valores inválidos em NaN, facilitando o tratamento de valores ausentes posteriormente.

A coluna Idoso foi convertida para inteiro (int64).

df = pd.read_csv("CHURN_TELECON_MOD08_TAREFA.csv", delimiter=';')
df['Pagamento_Mensal'] = pd.to_numeric(df['Pagamento_Mensal'], errors='coerce')
df['Total_Pago'] = pd.to_numeric(df['Total_Pago'], errors='coerce')
df['Idoso'] = df['Idoso'].astype('int64')

4️⃣ Verificação de valores faltantes

Foi calculado o percentual de valores ausentes em cada coluna para decidir o melhor tratamento.

missing_percent = df.isnull().mean() * 100
print(missing_percent)

Visualização de valores nulos
fig, axes = plt.subplots(3, 1, figsize=(12, 18))
msno.bar(df, ax=axes[0], fontsize=12, color='skyblue', sort='descending')
axes[0].set_title("Percentual de valores faltantes por coluna", fontsize=16)
msno.matrix(df, ax=axes[1], fontsize=12, sparkline=True)
axes[1].set_title("Distribuição de valores faltantes (matriz)", fontsize=16)
msno.heatmap(df, ax=axes[2], fontsize=12)
axes[2].set_title("Correlação entre valores faltantes nas colunas", fontsize=16)
plt.tight_layout()
plt.show()

5️⃣ Tratamento de valores faltantes
5.1 Imputação de valores

Variáveis numéricas: Pagamento_Mensal e Total_Pago → substituídas pela mediana, para reduzir o efeito de outliers.

Variáveis categóricas: Genero, Churn e Servico_Internet → substituídas pela moda, preservando a distribuição natural.

# Numéricas
df['Pagamento_Mensal'].fillna(df['Pagamento_Mensal'].median(), inplace=True)
df['Total_Pago'].fillna(df['Total_Pago'].median(), inplace=True)

# Categóricas
df['Genero'].fillna(df['Genero'].mode()[0], inplace=True)
df['Churn'].fillna(df['Churn'].mode()[0], inplace=True)
df['Servico_Internet'].fillna(df['Servico_Internet'].mode()[0], inplace=True)

# Outras colunas categóricas com nulos (se existirem)
df.fillna(df.mode().iloc[0], inplace=True)

5.2 Verificação da distribuição

Após a imputação, verificou-se se a substituição manteve a distribuição dos dados.

# Pagamento Mensal
sns.histplot(df['Pagamento_Mensal'], kde=True)
plt.title("Distribuição de Pagamento Mensal após imputação")
plt.show()

# Tipo de Internet
sns.countplot(x=df['Servico_Internet'], order=df['Servico_Internet'].value_counts().index)
plt.title("Distribuição de Tipo de Internet após imputação")
plt.show()

6️⃣ Padronização de valores inconsistentes

Espaços extras removidos

Letras maiúsculas/minúsculas uniformizadas

Valores como 'dsl', 'fiber optic' e 'no' padronizados

Colunas categóricas convertidas para Title Case

'No Internet Service' padronizado para 'No'

df['Servico_Internet'] = df['Servico_Internet'].str.strip().str.lower()
df['Servico_Internet'] = df['Servico_Internet'].replace({
    'dsl': 'DSL',
    'fiber optic': 'Fiber Optic',
    'no': 'No'
})

cols_cat = df.select_dtypes(include='object').columns
for col in cols_cat:
    df[col] = df[col].astype(str).str.strip().str.title()

colunas_padronizar = ['Servico_Seguranca', 'Suporte_Tecnico', 'StreamingTV']
df[colunas_padronizar] = df[colunas_padronizar].replace('No Internet Service', 'No')

7️⃣ Renomeação de colunas

As colunas foram renomeadas para facilitar a leitura e manter padrão consistente em português.

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

8️⃣ Conferência final

Não existem valores faltantes

Tipos de dados corretos

Estatísticas descritivas conferidas

print(df.isnull().sum())
print(df.info())
print(df.head())
print(df.describe(include='all'))

🔹 Observações finais

Mediana para colunas numéricas reduz o efeito de outliers.

Moda para colunas categóricas preserva a distribuição natural.

Pré-processamento garante dados limpos e consistentes, prontos para análise e modelagem preditiva.

Visualizações ajudam a justificar decisões de limpeza e substituição de valores faltantes.