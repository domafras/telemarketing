# Telemarketing

Cenário
- “Você provavelmente já recebeu uma ligação de telemarketing oferecendo um produto que você não precisa. Essa situação de estresse é minimizada quando você oferece um produto que o cliente realmente precisa.”
- Observação: y é a flag de cliente interessado no produto

Desafio técnico
- Problema de classificação binária


## Files

Disponíveis neste repositório
- Conjunto dos dados: train.csv
- Metadados: metadata.csv

## EDA
Análise exploratória do conjunto de dados trouxe alguns insights, como:
- dados ausentes sendo representados por -999 
- vários dados faltantes (colunas var60, var65, var66)
	- não se correlacionam fortemente com o alvo (y)
- várias features binárias
	- não apresentam redundância entre elas (multicolinearidade)
- ID apenas com valores únicos 
- apenas dados numéricos, independente do tipo raiz 
- dados desbalanceados
- ordem de magnitude discrepante

## Divisão e Cross-validation
Utilizando StratifiedKFold por conta da base ser desbalanceada (80-20)
- HistGradientBoostingClassifier()

## Pré-processamento
Após a divisão dos dados em treino e teste, foi aplicado aos dados de treino algumas transformações como:
- SimpleImputer
	- Estratégia da mediana para dados numéricos, evitando Outliers
	- Estratégia da moda para dados categóricos, imputando categoria mais frequente.
- MinMaxScaler, normalizando dados no intervalo de 0 a 1.
	- preparando aos modelos

*O tratamento foi feito após o split e somente nos dados de treino a fim de evitar vazamento dos dados (data leakage)

## Feature Selection
Utilizando a biblioteca Feature-Engine:
- DropConstantFeatures
	- baixa variância não é relavante ao modelo
- SmartCorrelatedSelection
	- correlação entre variáveis pode ser redundante
- SelectFromModel
	- FeatureImportance através de árvore (RandomForestClassifier)

## Resampling
A abordagem foi a RandomOverSampler, que gera novas amostrar para a classe minoritária (1).
- Antes do ROS: [9045, 2253]
- Depois do ROS: [9045, 9045]



## Grid Search
Foram avaliados alguns modelos
- Decision Tree
- HistGradientBoostingClassifier
- RandomForestClassifier
- KNeighborsClassifier

O parâmetro de scoring foi o 'recall' levando em consideração o contexto do problema. O melhor resultado foi da RandomForestClassifier, mas o tempo de execução desse modelo também foi o mais alto.


## Métricas
-   Utilização de métricas adequadas para validação de modelos de Machine Learning. O valor delas reflete a qualidade de um modelo, portanto, é crucial fazer as escolhas certas a fim de avaliar se o modelo atende aos requisitos. 
- A comparação entre a  **classe de predição do modelo**  e a  **classe real**  é a maneira de se avaliar um modelo.
-  **Matriz de confusão**  é uma forma de visualizar a distância do modelo para uma classificação perfeita, segue exemplo:

![Performance de Machine Learning - Matriz de Confusão - Diego Nogare](https://camo.githubusercontent.com/a5511e754b7e78a4547552c15a363bfb90022b986b776a18c8e5756302b45e2e/68747470733a2f2f646965676f6e6f676172652e6e65742f77702d636f6e74656e742f75706c6f6164732f323032302f30342f6d617472697a436f6e667573616f2d363030783338312e706e67)

No contexto desse projeto:
-   **TP:**  verdadeiro positivo (cliente tem interesse e modelo acertou)
-   **TN:**  verdadeiro negativo (cliente não tem interesse e o modelo acertou)
-   **FN**: falso negativo (modelo diz que não tem interesse, mas o valor real é interesse)
-   **FP:**  falso positivo (modelo diz que tem interesse, mas o valor real é não interesse)

Considerando:
-   **Acurácia**:  (TP + TN) / (TP + FP + FN + TN)
	- dentre todas as classificações, quantas estão corretas
-   **Precisão**: TP / (TP + FP)
	- dentre todas as classificações positivas (TP + FP), quantas realmente tem interesse
-   **Recall**: TP / (TP + FN)
	- dentre todas as situações reais positivas (TP + FN), quantas estão corretas (TP)
-   **F1-score**:
    -   2 * ((precisão * recall) / (precisão + recall))
    -   média harmônica entre precisão e recall

Portanto, para esse problema:
- Acurácia pode não ser interessante devido ao conjunto de dados ser desbalanceado (80% = 0). Ou seja, mesmo que o modelo classifique todos os exemplos como negativos, ainda terá alta acurácia.
- Precisão indica a proporção de exemplos classificados como interessados que realmente tem interesse.
- Recall é indicado à esse problema, pois considera os verdadeiros positivos e falsos negativos. Ou seja, quantos exemplos da classe positiva foram corretamente indicados, dentre todos os positivos reais.
- F1-score pode ser interessante para equilibrar as medidas de precisão e sensibilidade.

Concluindo, se o custo de perder um cliente em potencial for maior que o custo de entrar em contato com um cliente não interessado, é importante maximizar o **recall**. Por outro lado, se o custo de entrar em contato com um cliente não interessado for alto, é importante maximizar a precisão.

## Contato
Sem restrições ao uso, sinta-se à vontade para enviar sugestões.

Feito com  ❤️  por  [Leonardo Mafra](https://www.linkedin.com/in/leomafra/)

