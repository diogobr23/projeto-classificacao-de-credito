# Modelo de Concessão de Cartões de Crédito

## Introdução

Este repositório contém um código que aborda um problema de concessão de cartões de crédito. O objetivo é desenvolver um modelo de aprendizado de máquina capaz de prever se um solicitante será considerado um cliente "bom" ou "ruim" sem definir antecipadamente o que constitui um cliente "bom" ou "ruim". Este problema é uma tarefa desafiadora de classificação, com ênfase na análise de dados e na construção de um modelo preditivo.

## Contexto do Problema

O problema de concessão de cartões de crédito é fundamental para a indústria financeira. Os bancos usam informações pessoais e dados dos solicitantes de cartões de crédito para avaliar o risco de inadimplência e decidir se devem emitir um cartão de crédito. Tradicionalmente, esse processo é baseado em modelos estatísticos, como a regressão logística.

Com o avanço da aprendizagem de máquina, novas técnicas, como Boosting, Random Forest e Support Vector Machines, foram introduzidas na concessão de cartões de crédito. No entanto, esses modelos nem sempre são transparentes em suas decisões.

## Etapas do CRISP-DM Abordadas

### Etapa 1 - Entendimento do Negócio (CRISP-DM)

Nesta etapa, o problema é apresentado e contextualizado. É destacada a importância dos cartões de pontuação de crédito na indústria financeira e como esses modelos são tradicionalmente construídos com base em dados históricos.


### Etapa 2 - Entendimento dos Dados (CRISP-DM)

Um dicionário de dados é fornecido para explicar o significado de cada variável presente no conjunto de dados. Além disso, são realizadas análises univariadas e bivariadas para entender a distribuição das variáveis e suas relações com a variável resposta.

### Dicionário de Dados

| Variável          | Descrição                                         | Tipo     |
|-------------------|---------------------------------------------------|----------|
| sexo              | M = 'Masculino'; F = 'Feminino'                  | M/F      |
| posse_de_veiculo  | Y = 'possui'; N = 'não possui'                  | Y/N      |
| posse_de_imovel   | Y = 'possui'; N = 'não possui'                  | Y/N      |
| qtd_filhos        | Quantidade de filhos                             | inteiro  |
| tipo_renda        | Tipo de renda (ex: assalariado, autônomo etc)    | texto    |
| educacao          | Nível de educação (ex: secundário, superior etc)  | texto    |
| estado_civil      | Estado civil (ex: solteiro, casado etc)           | texto    |
| tipo_residencia   | Tipo de residência (ex: casa/apartamento, com os pais etc) | texto    |
| idade             | Idade em anos                                    | inteiro  |
| tempo de emprego  | Tempo de emprego em anos                         | inteiro  |
| possui_celular    | Indica se possui celular (1 = sim, 0 = não)       | binária  |
| possui_fone_comercial | Indica se possui telefone comercial (1 = sim, 0 = não) | binária  |
| possui_fone       | Indica se possui telefone (1 = sim, 0 = não)       | binária  |
| possui_email      | Indica se possui e-mail (1 = sim, 0 = não)         | binária  |
| qt_pessoas_residencia | Quantidade de pessoas na residência         | inteiro  |
| mau               | Indicadora de mau pagador (True = mau, False = bom) | binária  |



### Etapa 3 - Preparação dos Dados (CRISP-DM)

Nesta etapa, são realizadas operações de limpeza e pré-processamento nos dados, incluindo a conversão de variáveis categóricas em variáveis dummy. A preparação dos dados é fundamental para garantir que o modelo de aprendizado de máquina seja treinado com dados adequados.

#### Conversão de variáveis categóricas em dummies
```python
metadata = pd.DataFrame(df.dtypes, columns=['tipo'])
metadata['n_categorias'] = 0

for var in metadata.index:
    metadata.loc[var, 'n_categorias'] = len(df.groupby([var]).size())

def convert_dummy(df, feature, rank=0):
    pos = pd.get_dummies(df[feature], prefix=feature)
    mode = df[feature].value_counts().index[rank]
    biggest = feature + '_' + str(mode)
    pos.drop([biggest], axis=1, inplace=True)
    df.drop([feature], axis=1, inplace=True)
    df = df.join(pos)
    return df

for var in metadata[metadata['tipo'] == 'object'].index:
    df = convert_dummy(df, var)
```

### Etapa 4 - Modelagem (CRISP-DM)

Aqui, o modelo de aprendizado de máquina é construído usando a técnica de floresta aleatória. O conjunto de dados é dividido em treinamento e teste, e o modelo é treinado e avaliado com base na acurácia.

#### Treinando o modelo de Random Forest

```python
# Treinar uma Random Forest com 3 árvores
clf = RandomForestClassifier(n_estimators=3)
clf.fit(x_train, y_train)

# Calculando a acurácia
y_pred = clf.predict(x_test)
acc = metrics.accuracy_score(y_test, y_pred)
print('Acurácia: {0:.2f}%'.format(acc * 100))

# Matriz de confusão
tab = pd.crosstab(index=y_pred, columns=y_test)
print(tab.iloc[1][0] / (tab.iloc[1][0] + tab.iloc[0][0]))
print(tab.iloc[1][1] / (tab.iloc[1][1] + tab.iloc[0][1]))
```

### Etapa 5 - Avaliação dos Resultados (CRISP-DM)

A avaliação do modelo se concentra na acurácia do modelo em prever clientes "bons" e "maus". O objetivo é entender como o modelo se comporta em termos de acertos e erros de classificação.

### Etapa 6 - Implantação (CRISP-DM)

Embora não esteja detalhada no código, a implantação é a etapa final do processo CRISP-DM. Nesta etapa, o modelo desenvolvido pode ser implementado em um motor de concessão de crédito para automatizar decisões de aprovação e rejeição de cartões de crédito.

## Uso do Código

O código fornecido está estruturado em Python e utiliza a biblioteca scikit-learn para construir e avaliar o modelo de floresta aleatória. Para executar o código, siga as etapas apropriadas:

1. Certifique-se de que você tem as dependências necessárias instaladas, como pandas, seaborn e matplotlib.

2. Carregue os dados apropriados no formato correto. Os dados devem seguir o dicionário de dados fornecido.

3. Execute as etapas de limpeza e pré-processamento dos dados, conforme descrito no código.

4. Execute a modelagem, ajustando os hiperparâmetros, se necessário, e avalie o desempenho do modelo.

5. Analise os resultados e ajuste o modelo, conforme necessário.

## Conclusão

Este código aborda um problema crítico na indústria financeira: a concessão de cartões de crédito. O processo de construção do modelo de aprendizado de máquina é apresentado de acordo com as etapas do CRISP-DM, desde a compreensão do negócio até a avaliação dos resultados. A acurácia do modelo é utilizada como métrica de avaliação, mas é importante lembrar que outras métricas podem ser relevantes dependendo dos objetivos do negócio.

Este README fornece uma visão geral do código e do problema em questão.
