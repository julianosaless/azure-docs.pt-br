---
title: Implantar IoT Edge módulo de segurança
description: Saiba mais sobre como implantar uma central de segurança do Azure para o agente de segurança do IoT no IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 4dd7ca8f926862487b9505731c0662e68ee3d7c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311271"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Implantar um módulo de segurança em seu dispositivo IoT Edge

A **central de segurança do Azure para** o módulo IOT fornece uma solução de segurança abrangente para seus dispositivos IOT Edge.
O módulo de segurança coleta, agrega e analisa dados brutos de segurança do sistema operacional e do sistema de contêiner em alertas e recomendações de segurança acionáveis.
Para saber mais, consulte [módulo de segurança para IOT Edge](security-edge-architecture.md).

Neste artigo, você aprenderá a implantar um módulo de segurança em seu dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Implantar o módulo de segurança

Use as etapas a seguir para implantar um módulo de segurança da central de segurança do Azure para IoT para IoT Edge.

### <a name="prerequisites"></a>Pré-requisitos

1. No Hub IoT, verifique se o dispositivo está [registrado como um dispositivo IOT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

1. A central de segurança do Azure para IoT Edge módulo requer que a [estrutura auditada](https://linux.die.net/man/8/auditd) esteja instalada no dispositivo IOT Edge.

    - Instale a estrutura executando o seguinte comando em seu dispositivo de IoT Edge:

    `sudo apt-get install auditd audispd-plugins`

    - Verifique se a auditoria está ativa executando o seguinte comando:

    `sudo systemctl status auditd`<br>
    - A resposta esperada é:`active (running)`

### <a name="deployment-using-azure-portal"></a>Implantação usando portal do Azure

1. No portal do Azure, abra o **Marketplace**.

1. Selecione **Internet das coisas**, em seguida, pesquise a **central de segurança do Azure para IOT** e selecione-a.

   ![Selecione a central de segurança do Azure para IoT](media/howto/edge-onboarding-8.png)

1. Clique em **criar** para configurar a implantação.

1. Escolha a **assinatura** do Azure do Hub IOT e selecione o **Hub IOT**.<br>Selecione **implantar em um dispositivo** para direcionar um único dispositivo ou selecione **implantar em escala** para vários dispositivos de destino e clique em **criar**. Para obter mais informações sobre a implantação em escala, consulte [como implantar](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor).

    >[!Note]
    >Se você selecionou **implantar em escala**, adicione o nome do dispositivo e os detalhes antes de continuar para a guia **Adicionar módulos** nas instruções a seguir.

Conclua cada etapa para concluir sua implantação de IoT Edge para a central de segurança do Azure para IoT.

#### <a name="step-1-modules"></a>Etapa 1: módulos

1. Selecione o módulo **AzureSecurityCenterforIoT** .
1. Na guia **configurações do módulo** , altere o **nome** para **azureiotsecurity**.
1. Na guia **variáveis de ambiente** , adicione uma variável, se necessário (por exemplo, nível de depuração).
1. Na guia **Opções de criação do contêiner** , adicione a seguinte configuração:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. Na guia **configurações de configuração do módulo** , adicione a seguinte configuração:

    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
    ```

1. Selecione **Atualizar**.

#### <a name="step-2-runtime-settings"></a>Etapa 2: configurações de tempo de execução

1. Selecione **configurações de tempo de execução**.
1. Em **Hub do Edge**, altere a **imagem** para **MCR.Microsoft.com/azureiotedge-Hub:1.0.8.3**.
1. Verifique se a **opção criar opções** está definida com a seguinte configuração:

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

1. Selecione **Salvar**.

1. Selecione **Avançar**.

#### <a name="step-3-specify-routes"></a>Etapa 3: especificar rotas

1. Na guia **especificar rotas** , verifique se você tem uma rota (explícita ou implícita) que encaminhará as mensagens do módulo **azureiotsecurity** para **$upstream** de acordo com os exemplos a seguir. Somente quando a rota estiver em vigor, selecione **Avançar**.

   Roteiros de exemplo:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Selecione **Avançar**.

#### <a name="step-4-review-deployment"></a>Etapa 4: examinar a implantação

- Na guia **examinar implantação** , examine as informações de implantação e, em seguida, selecione **criar** para concluir a implantação.

## <a name="diagnostic-steps"></a>Etapas de diagnóstico

Se você encontrar um problema, os logs de contêiner serão a melhor maneira de aprender sobre o estado de um dispositivo de módulo de segurança IoT Edge. Use as ferramentas e os comandos desta seção para coletar informações.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Verifique se os contêineres necessários estão instalados e funcionando conforme o esperado

1. Execute o seguinte comando em seu dispositivo IoT Edge:

    `sudo docker ps`

1. Verifique se os seguintes contêineres estão em execução:

   | Name | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Se os contêineres mínimos necessários não estiverem presentes, verifique se o manifesto de implantação do IoT Edge está alinhado com as configurações recomendadas. Para obter mais informações, consulte [implantar IOT Edge Module](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Inspecione os logs de módulo para obter erros

1. Execute o seguinte comando em seu dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`

1. Para logs mais detalhados, adicione a seguinte variável de ambiente **azureiotsecurity** à implantação do módulo `logLevel=Debug`azureiotsecurity:.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as opções de configuração, vá para o guia de instruções para configuração de módulo.
> [!div class="nextstepaction"]
> [Guia de instruções de configuração de módulo](./how-to-agent-configuration.md)
