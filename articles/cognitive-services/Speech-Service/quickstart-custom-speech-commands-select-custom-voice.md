---
title: 'Início rápido: usar comandos personalizados com voz personalizada (visualização)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você especificará a voz de saída de um aplicativo de comandos personalizados.
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872450"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>Início rápido: usar comandos personalizados com voz personalizada (visualização)

No [artigo anterior](./quickstart-custom-speech-commands-create-parameters.md), criamos um novo projeto de comandos personalizados para responder a comandos com parâmetros.

Neste artigo, selecionaremos uma voz de saída personalizada para o aplicativo que criamos.

## <a name="select-a-custom-voice"></a>Selecionar uma voz personalizada

1. Abrir o projeto [que criamos anteriormente](./quickstart-custom-speech-commands-create-parameters.md)
1. Selecione **configurações** no painel esquerdo
1. Selecione **voz personalizada** no painel central
1. Selecione a voz pública ou personalizada desejada da tabela
1. Selecione **Salvar**

> [!div class="mx-imgBorder"]
> ![Sentenças de exemplo com parâmetros](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - Para **vozes públicas**, os **tipos neurais** estão disponíveis somente para regiões específicas. Para verificar a disponibilidade, consulte [vozes padrão e neural por região/ponto de extremidade](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - Para **vozes personalizadas**, elas podem ser criadas na página projeto de voz personalizado. Consulte [introdução à voz personalizada](./how-to-custom-voice.md).

Agora, o aplicativo responderá na voz selecionada, em vez da voz padrão.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Início rápido: conectar a um aplicativo de comando personalizado com o SDK de fala (versão prévia)](./quickstart-custom-speech-commands-speech-sdk.md)

