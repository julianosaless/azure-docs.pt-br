---
title: 'Avaliar modelo: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo modelo de avaliação no Azure Machine Learning para medir a precisão de um modelo treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/24/2020
ms.openlocfilehash: f69a3f61c288b320399d1b3abfc632c93261c540
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983356"
---
# <a name="evaluate-model-module"></a>Módulo avaliar modelo

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para medir a precisão de um modelo treinado. Você fornece um conjunto de resultados que contém pontuações geradas de um modelo e o módulo **avaliar modelo** computa um conjunto de métricas de avaliação padrão do setor.
  
 As métricas retornadas pelo **modelo de avaliação** dependem do tipo de modelo que você está avaliando:  
  
-   **Modelos de classificação**    
-   **Modelos de regressão**  
-   **Modelos de clustering**  


> [!TIP]
> Se você for novo na avaliação do modelo, recomendamos a série de vídeos de Dr. Stephen Elston, como parte do [curso do Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) do EdX. 


## <a name="how-to-use-evaluate-model"></a>Como usar o modelo de avaliação
1. Conecte a saída do conjunto de dados **pontuado** da saída do [modelo de Pontuação](./score-model.md) ou do conjunto de resultados do resultado de [assign data a clusters](./assign-data-to-clusters.md) à porta de entrada à esquerda do **modelo de avaliação**. 
  > [!NOTE] 
  > Se usar módulos, como "selecionar colunas no conjunto de dados", para selecionar parte do DataSet de entrada, verifique se a coluna de rótulo real (usada no treinamento), a coluna ' probabilidades de Pontuação ' e a coluna ' rótulos pontuados ' existem para calcular as métricas como AUC, a precisão da classificação binária/detecção de anomalias.
  > Coluna de rótulo real, a coluna ' rótulos pontuados ' existe para calcular métricas para classificação/regressão de várias classes.
  > Coluna ' assignments ', Columns ' DistancesToClusterCenter no. X ' (X é índice de centróide, variando de 0,..., número de centróides-1) existem para calcular métricas para clustering.

2. Adicional Conecte a saída do conjunto de dados **pontuado** da saída do [modelo de Pontuação](./score-model.md) ou do conjunto de resultados do resultado de Assign data a clusters para o segundo modelo à porta de entrada **à direita** do **modelo de avaliação**. Você pode comparar facilmente os resultados de dois modelos diferentes nos mesmos dados. Os dois algoritmos de entrada devem ser do mesmo tipo de algoritmo. Ou, você pode comparar classificações de duas execuções diferentes sobre os mesmos dados com parâmetros diferentes.

    > [!NOTE]
    > O tipo de algoritmo se refere a ' classificação de duas classes ', ' classificação multiclasse ', ' regressão ', ' clustering ' em ' algoritmos de Machine Learning '. 

3. Envie o pipeline para gerar as pontuações de avaliação.

## <a name="results"></a>Resultados

Depois de executar o **modelo de avaliação**, clique com o botão direito do mouse no módulo e selecione **Visualizar resultados da avaliação** para ver os resultados.

Se você conectar conjuntos de dados a ambas as entradas do **modelo de avaliação**, os resultados conterão métricas para ambos os conjuntos ou ambos os modelos.
O modelo ou os dados anexados à porta à esquerda são apresentados primeiro no relatório, seguidos pelas métricas para o conjunto de dados ou modelo anexado na porta correta.  

Por exemplo, a imagem a seguir representa uma comparação de resultados de dois modelos de clustering que foram criados nos mesmos dados, mas com parâmetros diferentes.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Como esse é um modelo de clustering, os resultados da avaliação são diferentes de se você comparasse pontuações de dois modelos de regressão ou compararam dois modelos de classificação. No entanto, a apresentação geral é a mesma. 

## <a name="metrics"></a>Métricas

Esta seção descreve as métricas retornadas para os tipos específicos de modelos com suporte para uso com o **modelo de avaliação**:

+ [modelos de classificação](#metrics-for-classification-models)
+ [modelos de regressão](#metrics-for-regression-models)
+ [modelos de clustering](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Métricas para modelos de classificação

As métricas a seguir são relatadas ao avaliar modelos de classificação.
  
-   A **precisão** mede a imsorte de um modelo de classificação como a proporção de resultados verdadeiros para casos totais.  
  
-   **Precisão** é a proporção de resultados verdadeiros em todos os resultados positivos.  
  
-   **Recall** é a fração de todos os resultados corretos retornados pelo modelo.  
  
-   A **Pontuação de f** é calculada como a média ponderada de precisão e RECALL entre 0 e 1, em que o valor ideal de Pontuação F é 1.  
  
-   **Auc** mede a área sob a curva plotada com verdadeiros positivos no eixo y e falsos positivos no eixo x. Essa métrica é útil porque fornece um único número que permite comparar modelos de tipos diferentes.  
  
- A **perda de log média** é uma única Pontuação usada para expressar a penalidade para resultados incorretos. Ele é calculado como a diferença entre duas distribuições de probabilidade – a verdadeira e a do modelo.  
  
- A **perda do log de treinamento** é uma única pontuação que representa a vantagem do classificador em uma previsão aleatória. A perda de log mede a incerteza de seu modelo comparando as probabilidades que gera para os valores conhecidos (verdade) nos rótulos. Você deseja minimizar a perda de log para o modelo como um todo.

### <a name="metrics-for-regression-models"></a>Métricas para modelos de regressão
 
As métricas retornadas para modelos de regressão são projetadas para estimar a quantidade de erros.  Um modelo é considerado adequado para os dados se a diferença entre os valores observados e previstos for pequena. No entanto, observar o padrão dos resíduos (a diferença entre qualquer ponto previsto e seu valor real correspondente) pode informá-lo muito sobre a possível diferença no modelo.  
  
 As métricas a seguir são relatadas para avaliar modelos de regressão.
  
- O **erro de média absoluta (Mae)** mede o quão próximo as previsões são para os resultados reais; Portanto, uma pontuação mais baixa é melhor.  
  
- **Erro de quadrado médio de raiz (RMSE)** cria um único valor que resume o erro no modelo. Ao elevar a diferença, a métrica não considera a diferença entre a previsão de excesso e a subprevisão.  
  
- **Erro absoluto relativo (RAE)** é a diferença absoluta relativa entre os valores esperado e real; relativo porque a diferença média é dividida pela média aritmética.  
  
- O **erro quadrado relativo (RSE)** normaliza, de forma semelhante, o erro quadrado total dos valores previstos dividindo pelo erro quadrado total dos valores reais.  
  

  
- O **coeficiente de determinação**, geralmente referido como R<sup>2</sup>, representa a potência preditiva do modelo como um valor entre 0 e 1. Zero significa que o modelo é aleatório (explica nada); 1 significa que há um ajuste perfeito. No entanto, cuidado deve ser usado na interpretação de valores de R<sup>2</sup> , já que valores baixos podem ser totalmente normais e valores altos podem ser suspeitos.

###  <a name="metrics-for-clustering-models"></a>Métricas para modelos de clustering

Como os modelos de clustering diferem significativamente dos modelos de classificação e regressão em muitos aspectos, o [modelo de avaliação](evaluate-model.md) também retorna um conjunto diferente de estatísticas para modelos de clustering.  
  
 As estatísticas retornadas para um modelo de clustering descrevem quantos pontos de dados foram atribuídos a cada cluster, a quantidade de separação entre clusters e quão rigidamente os pontos de dados são agrupados dentro de cada cluster.  
  
 As estatísticas para o modelo de clustering são calculadas em média em todo o conjunto de registros, com linhas adicionais contendo as estatísticas por cluster.  
  
As métricas a seguir são relatadas para avaliar modelos de clustering.
    
-   As pontuações na coluna, a **distância média para outro centro**, representam o quão próximo, em média, cada ponto no cluster é para as centróides de todos os outros clusters.   

-   As pontuações na coluna, a **distância média até o centro de cluster**, representam a proximidade de todos os pontos em um cluster para o centróide desse cluster.  
  
-   A coluna **número de pontos** mostra quantos pontos de dados foram atribuídos a cada cluster, juntamente com o número total geral de pontos de dados em qualquer cluster.  
  
     Se o número de pontos de dados atribuídos a clusters for menor que o número total de pontos de dados disponíveis, isso significará que não foi possível atribuir os pontos de dados a um cluster.  
  
-   As pontuações na coluna, a **distância máxima ao centro de cluster**, representam a soma das distâncias entre cada ponto e o centróide do cluster desse ponto.  
  
     Se esse número for alto, isso pode significar que o cluster é amplamente disperso. Você deve examinar essa estatística junto com a **distância média para o centro de cluster** para determinar a disseminação do cluster.   

-   A pontuação de **avaliação combinada** na parte inferior de cada seção de resultados lista as pontuações médias para os clusters criados nesse modelo específico.  
  

## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
