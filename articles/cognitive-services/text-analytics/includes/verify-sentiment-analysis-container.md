---
title: Verificar a instância de contêiner de Análise de Sentimento
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância de contêiner de Análise de Sentimento.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c9b5411c044bb45d284cac0d30705c2b3d40ccd0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876381"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Verificar a instância de contêiner de Análise de Sentimento

1. Selecione a guia **visão geral** e copie o endereço IP.
1. Abra uma nova guia do navegador e insira o endereço IP. Por exemplo, digite `http://<IP-address>:5000 (http://55.55.55.55:5000`). O home page do contêiner é exibido, o que permite que você saiba que o contêiner está em execução.

    ![Exibir o home page do contêiner para verificar se ele está em execução](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o link de **Descrição da API de serviço** para ir para a página do Swagger do contêiner.

1. Escolha qualquer uma das APIs **post** e selecione **experimentar**. Os parâmetros são exibidos, o que inclui esta entrada de exemplo:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Substitua a entrada pelo seguinte conteúdo JSON:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Defina **o** de status `true`como.

1. Selecione **executar** para determinar a suopinião do texto.

    O modelo que é empacotado no contêiner gera uma pontuação que varia de 0 a 1, em que 0 é uma opinião negativa e 1 é uma opinião positiva.

    A resposta JSON retornada inclui um sentimentos para a entrada de texto atualizada:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Agora podemos correlacionar o documento `id` dos dados JSON da carga de resposta ao documento `id`de carga de solicitação original. A pontuação de mais do `0.98` que indica um sentimentos muito positivo.