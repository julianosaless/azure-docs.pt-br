---
title: Tipo de entidade simples-LUIS
titleSuffix: Azure Cognitive Services
description: Uma entidade simples descreve um único conceito do contexto de aprendizado de máquina. Adicione uma lista de frases ao usar uma entidade simples para melhorar os resultados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1b5754be3c9941101a53f332841ace93caf9acdd
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684562"
---
# <a name="simple-entity"></a>Entidade simples

Uma entidade simples é uma entidade genérica que descreve um único conceito e é aprendida do contexto de aprendizado de máquina. Como entidades simples geralmente são nomes como nomes de empresas, nomes de produtos ou outras categorias de nomes, adicione uma [lista de frases](luis-concept-feature.md) ao usar uma entidade simples para aumentar o sinal dos nomes usados.

**A entidade é uma boa opção quando:**

* Os dados não são formatados consistentemente, mas indicam a mesma coisa.

![entidade simples](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>JSON de exemplo

`Bob Jones wants 3 meatball pho`

Na declaração anterior, `Bob Jones` é rotulado como uma entidade `Customer` simples.

Os dados retornados do ponto de extremidade incluem o nome da entidade, o texto descoberto da declaração, o local do texto descoberto e a pontuação:

#### <a name="v2-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V2](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-response"></a>[Resposta de ponto de extremidade de previsão V3](#tab/V3)

Esse é o JSON se `verbose=false` estiver definido na cadeia de caracteres de consulta:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Objeto de dados|Nome da entidade|Valor|
|--|--|--|
|Entidade simples|`Customer`|`bob jones`|

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda a sintaxe do padrão](reference-pattern-syntax.md)