---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "70034420"
---
## <a name="validate-that-a-container-is-running"></a>Validar se um contêiner está em execução 

Há várias maneiras de validar se um contêiner está em execução. Localize o endereço *IP externo* e a porta exposta do contêiner em questão e abra seu navegador da Web favorito. Use as várias URLs de solicitação abaixo para validar se o contêiner está em execução. As URLs de solicitação de exemplo listadas abaixo são `http://localhost:5000`, mas o seu contêiner específico pode variar. Tenha em mente que você depende do endereço *IP externo* do seu contêiner e da porta exposta.

| URL de Solicitação | Finalidade |
|--|--|
| `http://localhost:5000/` | O contêiner oferece uma home page. |
| `http://localhost:5000/status` | Solicitado com um HTTP GET, para validar que o contêiner está em execução sem causar uma consulta de ponto de extremidade. Essa solicitação pode ser usada para [testes de preparação e de execução](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) do Kubernetes. |
| `http://localhost:5000/swagger` | O contêiner fornece um conjunto completo de documentação para os pontos de extremidade e um recurso **Experimente** . Com esse recurso, é possível inserir suas configurações em um formulário HTML baseado na Web e realizar a consulta sem precisar escrever nenhum código. Após a consulta ser retornada, um exemplo de comando CURL será fornecido para demonstrar o formato do corpo e dos cabeçalhos HTTP exigidos. |

![Home page do contêiner](./media/cognitive-services-containers-api-documentation/container-webpage.png)
