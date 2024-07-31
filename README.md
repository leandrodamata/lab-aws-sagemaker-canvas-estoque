# 📊 Previsão de Estoque Inteligente na AWS com [SageMaker Canvas](https://aws.amazon.com/pt/sagemaker/canvas/)

## 📋 Pré-requisitos

Antes de começar, para realizar os mesmos passos desse projeto, certifique-se de ter uma conta na AWS. Se precisar de ajuda para criar sua conta, confira este repositório [AWS Cloud Quickstart](https://github.com/digitalinnovationone/aws-cloud-quickstart).

## 🚀 Passo a Passo da construção do projeto

### 1. Selecionando Dataset

-   Dentro do SageMaker Canvas selecionei a opção "Build a custom Project" para criar um modelo personalizado.
-   Dentre as opções disponíveis, visando realizar uma análise de previsão de estoque, selecionei o modelo "Predictive analysis", ou seja, análise preditiva.
-   Com um dataset já preparado em csv, https://github.com/leandrodamata/lab-aws-sagemaker-canvas-estoque/blob/main/datasets/dataset-1000-com-preco-variavel-e-renovacao-estoque.csv, fui em "Create dataset" e fiz o upload do meu dataset.

### 2. Construindo/treinando o modelo

-   Após realizar o upload do meu dataset, selecionei a coluna "QUANTIDADE_ESTOQUE" para analizar a previsão do nível do meu estoque.
-   Configurei o modelo para Time series forecasting, para realizar corretamente uma previsão de valores futuros com base em dados do passado, selecionando "ID_PRODUTO" como o id do meu produto para a configuração do modelo.
-   Por fim, segui algumas sugestões automáticas do Sagemaker para substituir os valores em branco de PRECO pela mediana e substitui também os valores em branco de QUANTIDADE_ESTOQUE para 0
-   Iniciei o treinamento do modelo com o Quick build para realizar um treinamento mais rápido.

### 3. Analisando os resultado após o treinamento do modelo

-   Após o treinamento, temos algumas métricas para analisar.

![image](https://github.com/user-attachments/assets/84fb94de-ba70-48a5-882c-581624ffb426)

-   Avg. wQL (Average Weighted Quantile Loss): avalia a previsão como um todo calculando a média da precisão em pontos de distribuição específicos chamados quantis para os quantis P10, P50 e P90. Como obtemos um valor de 0.346 temos um modelo consideravelmente preciso por estar proximo de 0%.
-   MAPE (Mean Absolute Percent Error): é o erro percentual (diferença percentual entre o valor médio previsto e o valor real) calculado em média em todos os pontos de tempo, portanto nosso valor de 0.971 é considerado com poucos erros por estar próximo de 0 também.
-   WAPE (Weighted Absolute Percent Error): mede o desvio geral dos valores previstos em relação aos valores observados e é definido pela soma do erro absoluto normalizado pela soma da meta absoluta. Novamente temos um valor baixo de 0.581 indicando precisão.
-   RMSE (Root Mean Square Error): é a raiz quadrada dos erros quadráticos médios. Aqui temos um valor um pouco acima de 36.006, indicando que podemos estar trabalhando com um modelo com muitos erros, mas considerando as demais medidas, podemos seguir a análise.
-   MASE (Mean Absolute Scaled Error): é a média do erro absoluto da previsão normalizada pelo erro médio absoluto de um método simples de previsão de linha de base. Com um valor < 1, sendo 0.852, temos um um modelo estimado como melhor que a linha de base.
-   Também temos a análise demonstrando que as coluna PRECO é a colunas com maior impacto na previsão, o que faz muito sentido, pensando que o preço geralmente é o principal motivo de uma venda, o que impacta na quantidade em estoque.

### 4. Previsões geradas pelo modelo

-   Primeiro vamos verificar como anaisar as imagens
-   Estamos analisando o nível de estoque de um item, em %, portanto os valores analisados são considerados de 0 a 100, como uma demanda histórica do nível de estoque
-   Nelas temos que para a previsão p10 (azul), espera-se que o valor real seja menor do que o valor previsto em 10% das vezes, e respectivamente para p50 (azul escuro), 50% das vezes e p90 (laranja), 90% das vezes


-   Para o item de ID = 1 temos a seguinte previsão gerada

![image](https://github.com/user-attachments/assets/e115ac55-e5b0-4b31-88cd-920ac2df6823)

-   O item 1 tem como demanda histórica um nível de estoque de 39, ou seja 39%
-   Neste caso, temos que para o item de ID = 1, o modelo indica que 10% das vezes, o valor real da previsao do estoque seja menor que 16.884%, 50% das vezes menor que 32.626% e 90% das vezes menor do que 98.948%,
-   Mesmo assim, não indica um grande tendencia de alta e nem de baixa


-   Para o item de ID = 18 temos a seguinte previsão gerada

![image](https://github.com/user-attachments/assets/1379ec04-c8bb-48c0-93ed-a364df827150)

-   Portanto, temos que para o item de ID = 18, com nível de estoque de 18%
-   Esperamos que 10% das vezes, o valor real da previsao seja menor que 36.707%, 50% das vezes menor que 62.205% e 90% das vezes menor do que 87.853%
-   Indicando uma tendência de alta no estoque deste item no geral, considerando na maioria das vezes ele será maior do que o valor atual do nível de estoque


-   Para o item de ID = 20 temos a seguinte previsão gerada

![image](https://github.com/user-attachments/assets/40bd9db5-cf3a-48bd-9e84-e77cb8a85e39)

-   Para este item de ID = 20, temos um nível de estoque de 100%
-   Então o modelo nos diz que 10% das vezes, o valor real da previsao seja menor que 27.882%, 50% das vezes menor que 66.456% e 90% das vezes menor do que 87.122%
-   Indicando uma tendência de baixa no estoque deste item, pois na maioria das vezes ele será maior do que o valor atual do nível de estoque considerando o modelo de Machine Learning atual.
