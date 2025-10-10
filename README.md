📊 Pré-Processamento da Base de Churn de Telecom

Aluno: Henry

📌 Sumário

🔹 Proposta da Atividade

1️⃣ Objetivo

2️⃣ Bibliotecas utilizadas

3️⃣ Carregamento da base e ajustes iniciais

4️⃣ Verificação de valores faltantes

5️⃣ Tratamento de valores faltantes

6️⃣ Visualizações após tratamento

7️⃣ Padronização de valores inconsistentes

8️⃣ Renomeação de colunas

9️⃣ Conferência final

🔟 Observações finais

🔹 Proposta da Atividade

Aplicação de pré-processamento de dados em uma base de churn de clientes de telecomunicações, preparando os dados para análise exploratória e modelagem preditiva.

Técnicas aplicadas:

🛠️ Técnica	🎯 Objetivo
Limpeza de dados	Remover inconsistências e valores inválidos
Tratamento de valores faltantes	Substituição por mediana/moda ou exclusão de linhas essenciais
Padronização de variáveis categóricas	Garantir uniformidade e consistência
Renomeação de colunas	Facilitar leitura e interpretação
Visualizações	Identificar padrões, outliers e distribuição de dados

💡 O pré-processamento garante que os dados estejam limpos, consistentes e confiáveis para análises e modelos preditivos.

1️⃣ Objetivo

Garantir que os dados estejam limpos, consistentes e prontos para modelagem preditiva.

Corrigir inconsistências.

Preencher valores ausentes com técnicas apropriadas (mediana e moda).

Padronizar colunas e categorias.

💡 Preparar os dados dessa forma evita erros em cálculos, análises estatísticas e modelagem preditiva.

2️⃣ Bibliotecas utilizadas

🐼 pandas: manipulação e limpeza de dados.

🔢 numpy: operações numéricas.

📊 matplotlib & seaborn: visualização de dados e análise de padrões.

🟦 missingno: análise de valores faltantes graficamente.

import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import missingno as msno

3️⃣ Carregamento da base e ajustes iniciais
df = pd.read_csv("CHURN_TELECON_MOD08_TAREFA.csv", delimiter=';')
df['Pagamento_Mensal'] = pd.to_numeric(df['Pagamento_Mensal'], errors='coerce')
df['Total_Pago'] = pd.to_numeric(df['Total_Pago'], errors='coerce')
df['Idoso'] = df['Idoso'].astype('int64')


💡 errors='coerce' transforma valores inválidos em NaN, facilitando o tratamento posterior.

4️⃣ Verificação de valores faltantes
missing_percent = df.isnull().mean() * 100
print(missing_percent)

📊 Visualização de valores nulos
fig, axes = plt.subplots(3, 1, figsize=(12, 18))
msno.bar(df, ax=axes[0], fontsize=12, color='skyblue', sort='descending')
axes[0].set_title("Percentual de valores faltantes por coluna", fontsize=16)
msno.matrix(df, ax=axes[1], fontsize=12, sparkline=True)
axes[1].set_title("Distribuição de valores faltantes (matriz)", fontsize=16)
msno.heatmap(df, ax=axes[2], fontsize=12)
axes[2].set_title("Correlação entre valores faltantes nas colunas", fontsize=16)
plt.tight_layout()
plt.show()


💡 Visualizações ajudam a decidir entre exclusão ou substituição de valores.

5️⃣ Tratamento de valores faltantes
5.1 ❌ Exclusão de linhas essenciais
df = df.dropna(subset=['Churn', 'Genero'])

5.2 ✅ Substituição de valores
# Numéricas
df['Pagamento_Mensal'].fillna(df['Pagamento_Mensal'].median(), inplace=True)
df['Total_Pago'].fillna(df['Total_Pago'].median(), inplace=True)

# Categóricas
df['Genero'].fillna(df['Genero'].mode()[0], inplace=True)
df['Churn'].fillna(df['Churn'].mode()[0], inplace=True)
df['Servico_Internet'].fillna(df['Servico_Internet'].mode()[0], inplace=True)

# Outras colunas categóricas
df.fillna(df.mode().iloc[0], inplace=True)

6️⃣ 📈 Visualizações após tratamento

Pagamento Mensal

sns.histplot(df['Pagamento_Mensal'], kde=True)
plt.title("Distribuição de Pagamento Mensal após imputação")
plt.show()


Tipo de Internet

sns.countplot(x=df['Servico_Internet'], order=df['Servico_Internet'].value_counts().index)
plt.title("Distribuição de Tipo de Internet após imputação")
plt.show()

7️⃣ Padronização de valores inconsistentes
# Internet
df['Servico_Internet'] = df['Servico_Internet'].str.strip().str.lower()
df['Servico_Internet'] = df['Servico_Internet'].replace({
    'dsl': 'DSL',
    'fiber optic': 'Fiber Optic',
    'no': 'No'
})

# Todas as colunas categóricas
cols_cat = df.select_dtypes(include='object').columns
for col in cols_cat:
    df[col] = df[col].astype(str).str.strip().str.title()

# Padronização adicional
colunas_padronizar = ['Servico_Seguranca', 'Suporte_Tecnico', 'StreamingTV']
df[colunas_padronizar] = df[colunas_padronizar].replace('No Internet Service', 'No')

8️⃣ 🏷️ Renomeação de colunas
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

9️⃣ ✅ Conferência final
print(df.isnull().sum())
print(df.info())
print(df.head())
print(df.describe(include='all'))

🔟 Observações finais

🧮 Mediana para colunas numéricas reduz efeito de outliers.

🏷️ Moda para colunas categóricas preserva distribuição natural.

❌ Exclusão de linhas apenas em colunas essenciais (Churn, Genero).

✅ Pré-processamento garante dados limpos e consistentes.

📊 Visualizações ajudam a justificar decisões de limpeza e substituição de valores faltantes.