---
title: Suporte de API nos Aplicativos Web Estáticos do Azure com Azure Functions
description: Saiba a quais recursos de API os Aplicativos Web Estáticos do Azure dão suporte
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 7f86ce9158b5b07b036c785c2973e8a5883ed686
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594265"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Suporte de API na Versão Prévia dos Aplicativos Web Estáticos do Azure com Azure Functions

Os Aplicativos Web Estáticos do Azure fornecem pontos de extremidade de API sem servidor via [Azure Functions](../azure-functions/functions-overview.md). Aproveitando as Azure Functions, as APIs são dimensionadas dinamicamente sob demanda e incluem os seguintes recursos:

- **Segurança integrada** com acesso direto aos dados de [ autenticação e autorização baseada em função](user-information.md) do usuário.
- **Roteamento contínuo** que torna a rota da _api_ disponível para o aplicativo web com segurança sem a necessidade de regras CORS personalizadas.
- **Azure Functions** v3 compatíveis com o Node.js 12.
- **Gatilhos HTTP** e associações de saída.

## <a name="configuration"></a>Configuração

Os pontos de extremidade da API estão disponíveis para o aplicativo web por meio da rota da _api_. Embora essa rota seja corrigida, você tem controle sobre a pasta onde localiza o aplicativo Azure Functions associado. Você pode alterar esse local [editando o arquivo YAML do fluxo de trabalho](github-actions-workflow.md#build-and-deploy) localizado na pasta _.github/workflows_ do repositório.

## <a name="constraints"></a>Restrições

Os Aplicativos Web Estáticos do Azure fornecem uma API por meio das Azure Functions. As funcionalidades das Azure Functions estão concentradas em um conjunto específico de recursos que permitem criar uma API para um aplicativo web, possibilitando que o aplicativo Web conecte a API com segurança. Esses recursos têm algumas restrições, incluindo:

- O prefixo de rota da API deve ser _api_.
- Gatilhos e associações são limitados ao [HTTP](../azure-functions/functions-bindings-http-webhook.md).
  - Todos os outros [gatilhos e associações das Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings), exceto as associações de saída, são restritos.
- Os logs só estarão disponíveis se você adicionar [Application Insights](../azure-functions/functions-monitoring.md) ao seu aplicativo Functions.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)
