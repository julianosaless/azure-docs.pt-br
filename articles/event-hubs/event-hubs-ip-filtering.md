---
title: Regras de firewall dos Hubs de Eventos do Azure| Microsoft Docs
description: Use as Regras de firewall para permitir conexões de endereços IP específicos com os Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 18212726f0ab921a05a3b640a32754c62958d047
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393135"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>Configurar regras de firewall IP para um namespace de hubs de eventos do Azure
Por padrão, os namespaces dos hubs de eventos podem ser acessados pela Internet, desde que a solicitação venha com autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Esse recurso é útil em cenários nos quais os hubs de eventos do Azure só devem ser acessados de determinados sites conhecidos. As regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, se você usar hubs de eventos com o [Azure Express Route][express-route], poderá criar uma **regra de firewall** para permitir o tráfego somente de seus endereços IP de infraestrutura local. 

>[!WARNING]
> Habilitar a filtragem de IP pode impedir que outros serviços do Azure interajam com os hubs de eventos.
>
> Não há suporte para serviços confiáveis da Microsoft quando as Redes Virtuais são implementadas.
>
> Cenários comuns do Azure que não funcionam com Redes Virtuais (observe que a lista **NÃO** é exaustiva):
> - Azure Monitor (configuração de diagnóstico)
> - Stream Analytics do Azure
> - Integração com a Grade de Eventos do Azure
> - Rotas do Hub IoT do Azure
> - Device Explorer do Azure IoT
>
> Os seguintes serviços da Microsoft devem estar em uma rede virtual
> - Aplicativos Web do Azure
> - Funções do Azure


## <a name="ip-firewall-rules"></a>Regras de firewall de IP
As regras de firewall IP são aplicadas no nível de namespace de hubs de eventos. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace dos Hubs de Eventos será rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar o portal do Azure para criar regras de firewall de IP para um namespace de hubs de eventos. 

1. Navegue até o **namespace dos hubs de eventos** na [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione a opção **rede** . Se você selecionar a opção **todas as redes** , o Hub de eventos aceitará conexões de qualquer endereço IP. Essa configuração é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall – opção todas as redes selecionada](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Para restringir o acesso a redes e endereços IP específicos, selecione a opção **redes selecionadas** . Na seção **Firewall** , siga estas etapas:
    1. Selecione a opção **Adicionar seu endereço IP do cliente** para fornecer ao IP do cliente atual o acesso ao namespace. 
    2. Para **intervalo de endereços**, insira um endereço IPv4 específico ou um intervalo de endereços IPv4 na notação CIDR. 
    3. Especifique se deseja **permitir que os serviços confiáveis da Microsoft ignorem esse firewall**. 

        ![Firewall – opção todas as redes selecionada](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Selecione **salvar** na barra de ferramentas para salvar as configurações. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do Portal.


## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager

> [!IMPORTANT]
> As regras de firewall têm suporte nas camadas **Standard** e **Dedicada** dos Hubs de Eventos. Não tem suporte na camada básica.

O modelo do Resource Manager a seguir permite adicionar uma regra de filtro IP a um namespace de Hubs de Eventos existente.

Parâmetros de modelo:

- A **ipMask** é um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24, representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, em que 24 indica o número de bits de prefixo significativos para o intervalo.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo do Azure Resource Manager tem a ação padrão definida como **"Allow"**, que não restringe as conexões.
> Ao criar as regras de rede virtual ou de firewalls, devemos alterar a ***"defaultAction"***
> 
> de
> ```json
> "defaultAction": "Allow"
> ```
> para
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implantar o modelo, siga as instruções para o [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Próximas etapas

Para restringir o acesso a Hubs de Eventos para redes virtuais do Azure, consulte o link a seguir:

- [Pontos de extremidade de serviço de rede virtual para Hubs de Eventos][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
