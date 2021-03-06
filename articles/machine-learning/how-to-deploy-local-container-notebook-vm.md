---
title: Como implantar modelos para computar instâncias
titleSuffix: Azure Machine Learning
description: Saiba como implantar seus modelos de Azure Machine Learning como um serviço Web usando instâncias de computação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 09164580b8bdb249fc12d14e827ad799d51cab34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756585"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Implantar um modelo para Azure Machine Learning instâncias de computação

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como usar Azure Machine Learning para implantar um modelo como um serviço Web em sua instância de computação do Azure Machine Learning. Use instâncias de computação se uma das seguintes condições for verdadeira:

- Você precisa implantar rapidamente e validar o modelo.
- Você está testando um modelo ainda em desenvolvimento.

> [!TIP]
> Implantar um modelo de um Jupyter Notebook em uma instância de computação para um serviço Web na mesma VM é uma _implantação local_. Nesse caso, o computador "local" é a instância de computação. Para obter mais informações sobre implantações, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um espaço de trabalho Azure Machine Learning com uma instância de computação em execução. Para obter mais informações, consulte [ambiente de instalação e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Implantar nas instâncias de computação

Um bloco de anotações de exemplo que demonstra implantações locais está incluído em sua instância de computação. Use as etapas a seguir para carregar o bloco de anotações e implantar o modelo como um serviço Web na VM:

1. No [Azure Machine Learning Studio](https://ml.azure.com), selecione suas instâncias de computação Azure Machine Learning.

1. Abra o `samples-*` subdiretório e, em seguida, `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`abra. Uma vez aberto, execute o bloco de anotações.

    ![Captura de tela do serviço local em execução no bloco de anotações](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. O notebook exibe a URL e a porta em que o serviço está sendo executado. Por exemplo, `https://localhost:6789`. Você também pode executar a célula que `print('Local service port: {}'.format(local_service.port))` contém para exibir a porta.

    ![Captura de tela da porta de serviço local em execução](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Para testar o serviço de uma instância de computação, use `https://localhost:<local_service.port>` a URL. Para testar a partir de um cliente remoto, obtenha a URL pública do serviço em execução na instância de computação. A URL pública pode ser determinada usando a fórmula a seguir; 
    * VM do notebook `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`:. 
    * Instância de computação `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`:. 

    Por exemplo, 
    * VM do notebook:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Instância de computação:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Teste o serviço

Para enviar dados de exemplo para o serviço em execução, use o código a seguir. Substitua o valor de `service_url` pela URL de da etapa anterior:

> [!NOTE]
> Ao autenticar para uma implantação na instância de computação, a autenticação é feita usando Azure Active Directory. A chamada para `interactive_auth.get_authentication_header()` no código de exemplo autentica você usando o AAD e retorna um cabeçalho que pode ser usado para autenticar o serviço na instância de computação. Para obter mais informações, consulte [Configurar a autenticação para Azure Machine Learning recursos e fluxos de trabalho](how-to-setup-authentication.md#interactive-authentication).
>
> Ao autenticar para uma implantação no serviço kubernetes do Azure ou em instâncias de contêiner do Azure, um método de autenticação diferente é usado. Para obter mais informações sobre o, consulte [Configurar a autenticação para Azure Machine Learning recursos e fluxos de trabalho](how-to-setup-authentication.md#web-service-authentication).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Próximas etapas

* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Usar o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)