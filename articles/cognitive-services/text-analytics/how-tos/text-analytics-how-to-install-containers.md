---
title: Instalar e executar contêineres-Análise de Texto
titleSuffix: Azure Cognitive Services
description: Como baixar, instalar e executar contêineres para Análise de Texto neste tutorial passo a passo.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: efe76323b4159af01f1eaf470d9c1833edd0a186
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702139"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalar e executar contêineres da Análise de Texto

> [!NOTE]
> * O contêiner para Análise de Sentimento v3 agora está disponível para o público geral. Os contêineres de extração de frases-chave e detecção de idioma estão disponíveis como uma visualização pública não restrita.
> * A vinculação de entidade e NER não estão disponíveis no momento como um contêiner.

Os contêineres permitem executar as APIs de análise de texto em seu próprio ambiente e são ótimos para seus requisitos específicos de segurança e governança de dados. Os contêineres de Análise de Texto fornecem processamento de idioma natural avançado sobre texto bruto e incluem três funções principais: análise de sentimentos, extração de frases-chave e detecção de idioma. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> A conta gratuita é limitada a 5.000 transações por mês e apenas os <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">tipos <span class="docon docon-navigate-external x-hidden-focus"></span> de preço</a> **gratuito** e **padrão** são válidos para contêineres. Para obter mais informações sobre taxas de solicitação de transação, consulte [limites de dados](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Pré-requisitos

Para executar qualquer um dos contêineres de Análise de Texto, você deve ter o computador host e os ambientes de contêiner.

## <a name="preparation"></a>Preparação

Você deve cumprir os seguintes pré-requisitos antes de usar os contêineres de Análise de Texto:

|Necessária|Finalidade|
|--|--|
|Mecanismo Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|Recurso de Análise de Texto |Para usar o contêiner, você precisará ter:<br><br>Um [recurso de análise de texto](../../cognitive-services-apis-create-account.md) do Azure para obter a chave de API e o URI de ponto de extremidade associados. Ambos os valores estão disponíveis nas páginas Visão Geral e Chaves da Análise de Texto no portal do Azure e são necessários para iniciar o contêiner.<br><br>**{Api_key}**: uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}**: o ponto de extremidade conforme fornecido na página **visão geral**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve as especificações mínimas e recomendadas para os contêineres de Análise de Texto. Pelo menos 2 gigabytes (GB) de memória são necessários e cada núcleo da CPU deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido. As transações permitidas por seção (TPS) também são listadas.

|  | Especificações mínimas do host | Especificações de host recomendadas | TPS mínimo | TPS máximo|
|---|---------|-------------|--|--|
| **Detecção de idioma, extração de frases-chave**   | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15 | 30|
| **Análise de Sentimento v3**   | 1 núcleo, 2 GB de memória | 4 núcleos, 8 GB de memória |15 | 30|

O núcleo da CPU e a memória correspondem às `--cpus` `--memory` configurações e, que são usadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

As imagens de contêiner para Análise de Texto estão disponíveis no registro de contêiner da Microsoft.

# <a name="sentiment-analysis-v3"></a>[Análise de Sentimento v3](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extração de Frases-chave (versão prévia)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[Detecção de Idioma (versão prévia)](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

***

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias.
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [Docker execute](https://docs.docker.com/engine/reference/commandline/run/) para executar os contêineres. O contêiner continuará a ser executado até que você o interrompa.

Substitua os espaços reservados abaixo pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave para o recurso de Análise de Texto. Você pode encontrá-lo na página de **ponto de extremidade e chave** do recurso, na portal do Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O ponto de extremidade para acessar o API de Análise de Texto. Você pode encontrá-lo na página de **ponto de extremidade e chave** do recurso, na portal do Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

> [!IMPORTANT]
> * os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substitua ou remova essa barra com base nos requisitos do sistema operacional de seu computador host. 
> * As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).
> * O contêiner de análise de opiniões v3 agora está disponível para o público geral, que retorna [Rótulos de sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) na resposta. A extração de frases-chave e os contêineres de detecção de idioma usam V2 da API e estão em versão prévia.

# <a name="sentiment-analysis-v3"></a>[Análise de Sentimento v3](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Extração de Frases-chave (versão prévia)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[Detecção de Idioma (versão prévia)](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta com base em REST.

Use o host, `http://localhost:5000`, para APIs de contêiner.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solução de problemas

Se você executar o contêiner com uma [montagem](../text-analytics-resource-container-config.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Cobrança

Os contêineres de Análise de Texto enviam informações de cobrança para Azure, usando um recurso _Análise de Texto_ na conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres de Análise de Texto. Em resumo:

* O Análise de Texto fornece três contêineres do Linux para o Docker, encapsulando vários recursos:
   * *Análise de Sentimento*
   * *Extração de Frases-chave (versão prévia)* 
   * *Detecção de Idioma (versão prévia)*
   
* Imagens de contêiner são baixadas de um MCR (Registro de Contêiner da Microsoft) no Azure.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar as operações em contêineres de análise de texto especificando o URI do host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de serviços cognitivas não enviam dados do cliente (por exemplo, o texto que está sendo analisado) à Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](../text-analytics-resource-container-config.md) para configurações
* Consulte [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md) para resolver problemas relacionados à funcionalidade.
